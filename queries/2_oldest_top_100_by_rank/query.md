## Which player has held the highest rank whilst simultaneously being the oldest player in the ATP Top 100?

```
with ranking_max_ages as (
    select
        r.ranking_date,
        max(age(r.ranking_date,p.birth_date)) as max_age 
    from
        rankings r 
    inner join
        players p 
    on 
        r.player_id=p.player_id 
    where
        pos<100 
    group by r.ranking_date
) select
    r.ranking_date,
    p.lastname,
    r.pos,
    rma.max_age 
from
    rankings r 
inner join
    players p 
on 
    r.player_id=p.player_id 
inner join
    ranking_max_ages rma 
on 
    rma.ranking_date=r.ranking_date and 
    max_age=age(r.ranking_date,p.birth_date) 
order by
    pos,
    r.ranking_date desc;
```