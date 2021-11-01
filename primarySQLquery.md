```SQL
SELECT -- Grab the initial data set to build our retained v unretained table
  date_joined,
  total_players,
  total_retained,
  ROUND((total_retained / total_players*100), 2) AS percentage
FROM (
  SELECT --count total players retained and unretained
    date_joined,
    COUNT(player ) AS total_players,
    SUM (retained) AS total_retained
  FROM (
    SELECT -- Find out if people played the game beyond the first 30 days
      glass.joined AS date_joined,
      glass.player_id AS player,
      CASE
        WHEN glass.last_game - glass.joined > 30 THEN 1
      ELSE
      0
    END
      AS retained
    FROM (
      SELECT -- grab the tables and data we need for the rest of our query and table
        MIN(joined) AS joined,
        p.player_id,
        MAX(day) AS last_game
      FROM
        `pro-tracker-329514.gamecompanydata.matches_info` AS m
      JOIN
        `pro-tracker-329514.gamecompanydata.player_info` AS p
      ON
        m.player_id = p.player_id
      GROUP BY
        p.player_id) AS glass)
  GROUP BY
    date_joined)