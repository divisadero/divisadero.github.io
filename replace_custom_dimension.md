# Replace custom dimension

This query modifies de value of a given customDimension stored with index = *n*.

The process to do so is as follows:
1. Unnest the ```customDimensions``` array. Note that we need to give this a new name or otherwise the query crashes.
2. From the unnested array ```hits.customDimensions``` select:
  * The index for all custom dimensions.
  * The default value for all custom dimension but the one we want to change.
  * A *new_value* for the custom dimension with index *n*.
3. Replace the original ```customDimensions``` array with a new array with the new values-
4. Replace the initial ```hits``` array with a new array where the custom dimensions are modified.

```sql
#standardSQL
UPDATE `tablename`
SET hits = 
  # Create the new array to replace the current hits array
  ARRAY(
    SELECT AS STRUCT * REPLACE(
      # Inside of hits, replace the array customDimensions
      # with a new array
      ARRAY(
        SELECT AS STRUCT cd.index,
          # Select the default values for all indexes but index_number
          CASE WHEN cd.index = n THEN 'new value'
          ELSE cd.value
          END AS 
        FROM UNNEST(customDimensions) AS cd
      ) AS customDimensions)
    FROM UNNEST(hits) hit
  )
WHERE TRUE
```
<br/>
Note that this can take a while to run.
