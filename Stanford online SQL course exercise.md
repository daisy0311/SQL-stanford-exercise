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
```
