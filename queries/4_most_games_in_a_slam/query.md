## Which player has played the most games in a single Grand Slam?

```
WITH matches_with_games AS
  (SELECT *,
     (SELECT sum(s::int)
      FROM unnest(array_remove(regexp_split_to_array(regexp_replace(score,'\(.+\)',''),'(\s|-|\(|\)|[A-Za-z]|\/)+'),'')) s) AS games
   FROM matches
   WHERE tourney_level='G'
     AND (score NOT LIKE '%RET')
     AND (score NOT LIKE '%unfinished')
     AND (score NOT LIKE '%DEF')
     AND (score NOT LIKE '%W/O')
     AND (score NOT LIKE '%NA')
     AND (score NOT LIKE '%ABD')),
     e_matches AS (
                     (SELECT *,
                             winner_name AS player_name
                      FROM matches_with_games
                      WHERE tourney_level='G')
                   UNION
                     (SELECT *,
                             loser_name AS player_name
                      FROM matches_with_games
                      WHERE tourney_level='G'))
SELECT tourney_name,
       tourney_id,
       player_name,
       sum(games)
FROM e_matches
GROUP BY 1,
         2,
         3
ORDER BY 4 DESC LIMIT 10;
```