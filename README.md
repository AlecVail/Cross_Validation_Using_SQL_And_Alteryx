# Mining-Data-with-SQL-and-Alteryx
Wipro Ltd. - Suspense Account Reconciliation

Alteryx Snapshot

This is a screenshot of my entire workflow sectionalized into three different categories of validation: IRS, E820, CDS

![image](https://user-images.githubusercontent.com/100732722/233162092-fa56197b-217b-4dfc-83d2-2c2dbf2192f5.png)

Step 1 - Validate IRS Table

![IRS](https://user-images.githubusercontent.com/100732722/233165782-bc7f753e-3000-4837-8736-810dc37f3464.png)

The data table that holds all our suspense records is called "IRS". In this section of the workflow, on the left, you can see a grey circle with an open-book symbol. That tool is a SQL query that pulls all records from the IRS table that fit the criteria in my previously made "Load File".

        Select *
        From db2prod.IRS_PYMT_XREF
        Where IRS_PYMT_XREF_ID in ('HHHH')

I then join all IRS data with my Load File. This blended data is now going to be compared with the 2nd table, the "E820".
