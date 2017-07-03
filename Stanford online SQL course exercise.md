# SQL-stanford-exercise
## SQL Movie-Rating Query Exercises Extras
1. Find the names of all reviewers who rated Gone with the Wind. 
```sql
select distinct name
from reviewer join rating on reviewer.rID = rating.rID
join movie on rating.mID = movie.mID
where title = 'Gone with the Wind';
```
2. For any rating where the reviewer is the same as the director of the movie, return the reviewer name, movie title, and number of stars. 
```sql
select name, title, stars
from movie join rating on movie.mID = rating.mID
join reviewer on rating.rID = reviewer.rID
where name = director;
```
3. Return all reviewer names and movie names together in a single list, alphabetized. (Sorting by the first name of the reviewer and first word in the title is fine; no need for special processing on last names or removing "The".)
```sql
select name
from reviewer
union
select title
from movie;
```
4. Find the titles of all movies not reviewed by Chris Jackson. 
```sql
select title
from movie
where mID not in (
select movie.mID
from movie join rating on movie.mID = rating.mID
join reviewer on rating.rID = reviewer.rID
where name = 'Chris Jackson');
```
5. For all pairs of reviewers such that both reviewers gave a rating to the same movie, return the names of both reviewers. Eliminate duplicates, don't pair reviewers with themselves, and include each pair only once. For each pair, return the names in the pair in alphabetical order. 
```sql
select name, title, stars, ratingDate
from reviewer re left join rating r on re.rID = r.rID
left join movie m on m.mID = r.mID
order by name, title, stars;
```
6. For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie. 
```sql
select name, title
from reviewer, movie, (
select R1.rID, R1.mID
from rating R1, rating R2 
where R1.rID = R2.rID and R1.mID = R2.mID and R1.ratingDate > R2.ratingDate and R1.stars > R2.stars) T
where reviewer.rID = T.rID and movie.mID = T.mID;
```
7. For each movie that has at least one rating, find the highest number of stars that movie received. Return the movie title and number of stars. Sort by movie title. 
```sql
select title, max(stars)
from movie join rating on movie.mID = rating.mID
group by movie.mID
order by title;
```
8. For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie title. 
```sql
select title, max(stars) - min(stars) as rating_spread
from movie join rating on movie.mID = rating.mID
group by movie.mID
order by rating_spread desc, title;
```
9. Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.) 
```sql
select avg(B.average) - avg(A.average)
from (
select year, avg(stars) as average
from movie join rating on movie.mID = rating.mID
group by movie.mID
having year < 1980)  B, (
select year, avg(stars) as average
from movie join rating on movie.mID = rating.mID
group by movie.mID
having year > 1980)  A;
```
## SQL Movie-Rating Query Exercises Extras
1. Find the names of all reviewers who rated Gone with the Wind. 
```sql
select distinct name
from reviewer join rating on reviewer.rID = rating.rID
join movie on rating.mID = movie.mID
where title = 'Gone with the Wind';
```
2. For any rating where the reviewer is the same as the director of the movie, return the reviewer name, movie title, and number of stars. 
```sql
select name, title, stars
from movie join rating on movie.mID = rating.mID
join reviewer on rating.rID = reviewer.rID
where name = director;
```
3. Return all reviewer names and movie names together in a single list, alphabetized. (Sorting by the first name of the reviewer and first word in the title is fine; no need for special processing on last names or removing "The".) 
```sql
select name
from reviewer
union
select title
from movie;
```
4. Find the titles of all movies not reviewed by Chris Jackson. 
```sql
select title
from movie
where mID not in (
select movie.mID
from movie join rating on movie.mID = rating.mID
join reviewer on rating.rID = reviewer.rID
where name = 'Chris Jackson'); 
```
5. For all pairs of reviewers such that both reviewers gave a rating to the same movie, return the names of both reviewers. Eliminate duplicates, don't pair reviewers with themselves, and include each pair only once. For each pair, return the names in the pair in alphabetical order. 
```sql
select distinct R1.name, R2.name
from (rating join reviewer on rating.rID = reviewer.rID) R1
, (rating join reviewer on rating.rID = reviewer.rID) R2
where R1.mID = R2.mID and R1.name < R2.name
order by R1.name, R2.name;
```
6. For each rating that is the lowest (fewest stars) currently in the database, return the reviewer name, movie title, and number of stars. 
```sql
select name, title, stars
from rating join reviewer on rating.rID = reviewer.rID
join movie on rating.mID = movie.mID
where stars = (select min(stars) from rating);
```
7. List movie titles and average ratings, from highest-rated to lowest-rated. If two or more movies have the same average rating, list them in alphabetical order. 
```sql
select title, avg(stars)
from rating join movie on rating.mID = movie.mID
group by movie.mID
order by avg(stars) desc, title;
```
8. Find the names of all reviewers who have contributed three or more ratings. (As an extra challenge, try writing the query without HAVING or without COUNT.) 
```sql
select name
from reviewer join (
select rating.rID, SUM(1) as c
from rating 
group by rID) R on R.rID = reviewer.rID
where R.c >= 3;
```
9. Some directors directed more than one movie. For all such directors, return the titles of all movies directed by them, along with the director name. Sort by director name, then movie title. (As an extra challenge, try writing the query both with and without COUNT.) 
```sql
select title, director
from movie
where director in (select director from movie group by director having count(*) > 1)
order by director, title;
```
10. Find the movie(s) with the highest average rating. Return the movie title(s) and average rating. (Hint: This query is more difficult to write in SQLite than other systems; you might think of it as finding the highest average rating and then choosing the movie(s) with that average rating.) 
```sql
select title, avg(stars)
from movie join rating on movie.mID = rating.mID
group by title
having avg(stars) >= (select max(T.s) from (
select avg(stars) as s
from movie join rating on movie.mID = rating.mID
group by title) T);
```
11. Find the movie(s) with the lowest average rating. Return the movie title(s) and average rating. (Hint: This query may be more difficult to write in SQLite than other systems; you might think of it as finding the lowest average rating and then choosing the movie(s) with that average rating.) 
```sql
select title, avg(stars)
from movie join rating on movie.mID = rating.mID
group by title
having avg(stars) = (select min(T.s) from (
select avg(stars) as s from movie join rating on movie.mID = rating.mID group by title) T);
```
12. For each director, return the director's name together with the title(s) of the movie(s) they directed that received the highest rating among all of their movies, and the value of that rating. Ignore movies whose director is NULL. 
```sql
select director, title, max(stars)
from movie join rating on movie.mID = rating.mID
where director is not NULL 
group by director;
```


## SQL Social-Network Query Exercises
1. Find the names of all students who are friends with someone named Gabriel. 
```sql
select H2.name
from highschooler H1 join friend on H1.ID = friend.ID1 
join highschooler H2 on H2.ID = friend.ID2
where H1.name = 'Gabriel';
```
2. For every student who likes someone 2 or more grades younger than themselves, return that student's name and grade, and the name and grade of the student they like. 
```sql
select H1.name, H1.grade, H2.name, H2.grade
from highschooler H1 join likes on H1.ID = likes.ID1 
join highschooler H2 on H2.ID = likes.ID2
where H1.grade >= H2.grade + 2;
```
3. For every pair of students who both like each other, return the name and grade of both students. Include each pair only once, with the two names in alphabetical order. 
```sql
select H1.name, H1.grade, H2.name, H2.grade
from (
select L1.ID1 as ID1, L1.ID2 as ID2
from likes L1, likes L2
where L1.ID1 = L2.ID2 and L1.ID2 = L2.ID1) M
join highschooler H1 on H1.ID = M.ID1 
join highschooler H2 on H2.ID = M.ID2
where H1.name < H2.name;
```
4. Find all students who do not appear in the Likes table (as a student who likes or is liked) and return their names and grades. Sort by grade, then by name within each grade. 
```sql
select name, grade
from highschooler
where ID not in (
select ID1 from likes
union 
select ID2 from likes)
order by grade, name;
```
5. For every situation where student A likes student B, but we have no information about whom B likes (that is, B does not appear as an ID1 in the Likes table), return A and B's names and grades. 
```sql
select h1.name, h1.grade, h2.name, h2.grade
from highschooler h1, highschooler h2, likes
where h1.id = id1 and h2.id = id2 and h2.id not in (select id1 from likes);
```
6. Find names and grades of students who only have friends in the same grade. Return the result sorted by grade, then by name within each grade.
```sql
select h1.name, h1.grade
from highschooler h1 join friend on h1.ID = friend.ID1 
join highschooler h2 on h2.ID = friend.ID2
group by h1.ID
having count(distinct h2.grade) = 1 and h1.grade = h2.grade
order by h1.grade, h1.name;
```
7. For each student A who likes a student B where the two are not friends, find if they have a friend C in common (who can introduce them!). For all such trios, return the name and grade of A, B, and C. 
```sql
select distinct h1.name, h1.grade, h2.name, h2.grade, h3.name, h3.grade
from highschooler h1, highschooler h2, highschooler h3, friend f1, friend f2, likes
where h1.ID = f1.ID1 and h3.ID = f1.ID2 and h2.ID = f2.ID1 and h3.ID = f2.ID2
and h1.ID = likes.ID1 and h2.ID = likes.ID2
and h2.ID not in (select friend.ID1 from friend where friend.ID2 = h1.ID);
```
