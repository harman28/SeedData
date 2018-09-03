## Which player has played the most 5 setters?

```
WITH five_set_matches AS
  (SELECT *
   FROM matches
   WHERE
     SETS=5
     AND (score LIKE '%-%-%-%-%-%')),
     wins AS
  (SELECT winner_name AS name,
          sum(1) AS wins,
          0 AS losses,
          count(*)
   FROM five_set_matches
   GROUP BY 1),
     losses AS
  (SELECT loser_name AS name,
          0 AS wins,
          sum(1) AS losses,
          count(*)
   FROM five_set_matches
   GROUP BY 1),
     wins_and_losses AS
  (SELECT name,
          wins,
          losses,
          COUNT
   FROM wins
   UNION SELECT name,
                wins,
                losses,
                COUNT
   FROM losses)
SELECT name,
       sum(wins+losses) AS total,
       sum(wins) AS wins,
       sum(losses) AS losses
FROM wins_and_losses
GROUP BY 1
ORDER BY total DESC LIMIT 10;
```