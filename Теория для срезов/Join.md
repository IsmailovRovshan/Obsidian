```
SELECT s."Id", s."FirstName", s."LastName", s."Patronymic", 
       s."Position", s."OrganizationId",
       o."Name" as "orgName"
FROM public."Subjects" s
INNER JOIN public."Organizations" o ON s."OrganizationId" = o."Id";
```
