```SQL
--SQL Query 1
SELECT -- final query to build percentage table
  location,
  total_players,
  total_retained,
  ROUND((100*total_retained / total_players),2) AS percentage,

FROM (
  SELECT --query to calculate totals we need
    location,
    COUNT(player ) AS total_players,
    SUM (retained) AS total_retained

  FROM (
    SELECT--query to connect >30 days
      glass.joined AS date_joined,
      glass.player_id AS player,
      location,
      CASE
        WHEN glass.last_game - glass.joined >30 THEN 1
      ELSE
      0
    END
      AS retained
    FROM (
      SELECT -- initial table to grab and join the tables we need
        MIN(joined) AS joined,
        p.player_id,
        location,
        MAX(m.day) AS last_game,
     FROM
        `pro-tracker-329514.gamecompanydata.matches_info` AS m
      JOIN
        `pro-tracker-329514.gamecompanydata.player_info` AS p
      ON
        m.player_id = p.player_id
     WHERE joined <335
      GROUP BY
        p.player_id,
        location) AS glass)
        
  GROUP BY
    location -- generate insights based on region
     )