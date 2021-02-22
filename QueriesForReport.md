# Queries for Reports

These queries are for the reports specified in the [Making A Report](MakingAReport.md) page.

## Sum of all dry weights of devices by class and category

```SQL
SELECT sum(d.dry_weight), d.ECClassId, cat.codevalue category, count(*) 
  FROM processphysical.device d 
  JOIN bis.category cat ON d.Category.Id = cat.ECInstanceId 
  GROUP BY d.category.id, d.ECClassId
```

## Identify devices that have no dry weight or have dry weight set to zero

```SQL
SELECT * FROM ProcessPhysical.DEVICE WHERE DRY_WEIGHT IS NULL OR DRY_WEIGHT = 0
```

## All Functional Elements and their corresponding Physical Element

```SQL
SELECT 
  ec_classname(f.ECClassId) functionalClass, f.Description functional_description, f.UserLabel functional_Label, 
  ec_classname(p.ECClassId) physicalClass, p.description physical_description, p.userlabel physical_Label 
FROM Functional.PhysicalElementFulfillsFunction pff 
  JOIN ProcessPhysical.NAMED_ITEM p ON p.ECInstanceId = pff.SourceECInstanceId 
  JOIN ProcessFunctional.NAMED_ITEM f on f.ECInstanceId = pff.TargetECInstanceId
```

## All functional or physcial elements without a corresponding physical or functional element

Physical Element

```SQL
SELECT * FROM ProcessPhysical.NAMED_ITEM WHERE ECInstanceId NOT IN (SELECT SourceECInstanceId FROM Functional.PhysicalElementFulfillsFunction)
```

Functional Element

```SQL
SELECT * FROM ProcessFunctional.NAMED_ITEM WHERE ECInstanceId NOT IN (SELECT TargetECInstanceId FROM Functional.PhysicalElementFulfillsFunction)
```