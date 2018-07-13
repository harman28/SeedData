## Which player has played the most games en route to a Grand Slam final?

```
WITH finalists AS
  (SELECT tourney_id,
          winner_id,
          loser_id
   FROM matches
   WHERE tourney_level='G'
     AND round='F'),
     matcheses AS
  (SELECT *,

     (SELECT sum(s::int)
      FROM unnest(array_remove(regexp_split_to_array(regexp_replace(score,'\(.+\)',''),'(\s|-|\(|\)|[A-Za-z]|\/)+'),'')) s) AS games
   FROM matches
   WHERE tourney_level='G')
SELECT m.winner_name,
       extract(YEAR
               FROM max(m.match_date)) AS YEAR,
       m.tourney_name,
       sum(str_count(m.score,'-')) AS
SETS,
       sum(games) AS games,
       array_agg(m.score) AS road
FROM matcheses m
INNER JOIN finalists f ON (m.tourney_id=f.tourney_id
                           AND (m.winner_id=f.winner_id
                                OR m.winner_id=f.loser_id))
WHERE m.tourney_level='G'
  AND m.round!='F'
  AND draw_size=128
GROUP BY m.winner_name,
         m.tourney_id,
         tourney_name
ORDER BY 5 DESC LIMIT 15;
```