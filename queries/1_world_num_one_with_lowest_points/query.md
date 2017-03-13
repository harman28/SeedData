## Which #1 ranked player held the lowest fraction of all available points?

```sql
WITH max_frac_by_date AS (SELECT
        ranking_date,
        MAX(pts),
        SUM(pts),
        TRUNC((MAX(pts)*100::FLOAT/SUM(pts))::NUMERIC, 2) AS frac     
    FROM
        rankings r     
    GROUP BY
        ranking_date     
    HAVING
        MAX(pts)>10) -- Sanity check, I have some strange values there
SELECT
    ROW_NUMBER () OVER (ORDER BY frac),
    p.lastname,
    r.ranking_date,
    m.frac 
FROM
    max_frac_by_date m 
INNER JOIN
    rankings r         
        ON m.ranking_date=r.ranking_date         
        AND m.max=r.pts 
INNER JOIN
    players p         
        ON r.player_id=p.player_id 
ORDER BY
    frac 
LIMIT 1;
```