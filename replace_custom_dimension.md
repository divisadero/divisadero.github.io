# Replace custom dimension

To update the value of a custom dimension you need to go two levels deep into nested fields, both the ```hits``` field and the ```customDimensions``` fields are arrays.
To modify the value a ```CASE``` clause is used, since the ```REPLACE``` clause doesn't accept a condition on a different field than the one to modify.

```
#standardSQL
UPDATE `tablename`
SET hits = 
  ARRAY(
    SELECT AS STRUCT * REPLACE(
      ARRAY(
        SELECT AS STRUCT cd.index,
          CASE WHEN cd.index = index_number THEN 'new value'
          ELSE cd.value
          END AS 
        FROM UNNEST(customDimensions) AS cd
      ) AS customDimensions)
    FROM UNNEST(hits) hit
  )
WHERE TRUE
```
