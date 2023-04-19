# Mining-Data-with-SQL-and-Alteryx
Wipro Ltd. - Suspense Account Reconciliation

This is a screenshot of my entire workflow sectionalized into three different categories of validation: IRS, E820, CDS

![image](https://user-images.githubusercontent.com/100732722/233162092-fa56197b-217b-4dfc-83d2-2c2dbf2192f5.png)

STEP 1 - Validate IRS Table

![IRS](https://user-images.githubusercontent.com/100732722/233165782-bc7f753e-3000-4837-8736-810dc37f3464.png)

The data table that holds all our suspense records is called "IRS". In this section of the workflow, on the left, you can see a grey circle with an open-book symbol. That tool is a SQL query that pulls all records from the IRS table that fit the criteria in my previously made "Load File".

        Select *
        From db2prod.IRS_PYMT_XREF
        Where IRS_PYMT_XREF_ID in ('HHHH')

The orange summary tool concatenates all IRS_PYMT_XREF_IDs I need to validate. These cases are then pushed through the dynamic input and replace the 'HHHH' placeholder when the workflow is ran. I then join all IRS data with my Load File. This blended data is now going to be compared with the 2nd table, the "E820".

STEP 2 - Validate E820

![E820](https://user-images.githubusercontent.com/100732722/233168066-fb927b44-66e0-45f4-8d52-8bd4baed7463.png)

As you can see I have another SQL query on the left (grey circle with book symbol). This query pulls from the E820 table. The E820 table tells us what all of our policies need to look like when fully reconciled. 

        Select PROD_LVL_OWNING_CARRIER, BILL_PERIOD, DETAIL_CASE_NUM, IDENT_CODE, REMIT_DTL_MONETARY_AMT, cast(ADDED_DATE as Date) as "TS Date"
        From db2prod.E820_APTC_PAYMENT_DETAIL
        Where STATUS = 'COMPLETE'
        And REMIT_DTL_REF_ID in ('APTC','APTCADJ')
        And IDENT_CODE in ('XXXX')

In this section (from left to right) I am cleansing all relevant/irrelevent data and concatenating all policy IDs to be searched into the E820 Query so I pull ONLY the data being validated, not the entire table. I then do a vlookup (purple tool on the far right) to combine the E820 and IRS data together. At this point I now know what each suspense record must look like after cash has been moved and the account is fully reconciled. 

STEP 3 - Validate CDS

![CDS](https://user-images.githubusercontent.com/100732722/233168467-c5ad0702-5d3b-40fa-8fe7-8c0f65a7cedd.png)

Next we have the CDS table. CDS is short for "Cash Distribution". This table shows us the detailed billing of each account/policy i.e. How much it was billed, when it was billed, how much the account collected, adjusted, net amount, etc.. Now we must confirm that when we move the money, the collected amount will match the amount shown in the E820 (the previous table). I then repeat the same steps as the previous section; Cleanse, concatenate, pull data from the table, and join the CDS data with my Load File, IRS, and E820 data.

        Select *
        From db2prod.CASH_DISTRIBUTION
        Where CASE_NUM in ('XXXX')
        
Once this section has been completed, we will then create a formula to dictate whether moving cash from a suspense account would be accurate and appropriate.
        
STEP 4 - REVISE ORIGINAL FILE

![Load File Creator](https://user-images.githubusercontent.com/100732722/233169038-51ebcc40-bc41-4a31-b8c4-53154f893022.png)

Here I have created a formula....
       
       [cash in IRS] + [cash in CDS] = [SUM Cash Amount in E820]. 

This means if there is a suspense record that does not align with all three data tables AFTER cash has been moved, it will not be included in the cash moves from suspense to full reconciliation and must be evaluated manually. In the results panel at the bottom it will show the correct "Load File" that needs to be submitted for reconciliation. Confidentiality dictates black highlighting in the picture shown. 
