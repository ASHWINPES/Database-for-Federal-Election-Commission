Problem Statement: 

The Federal Election Commission (FEC) is responsible for enforcing the campaign finance laws in the United States. As part of this responsibility, the FEC collects and publishes data on campaign contributions and expenditure. This data is used by journalists, researchers, and the public to monitor the influence of money in politics. The goal of this project is to create a comprehensive database of campaign finance data for the 2022-2023 election cycle. This database should be easily searchable and accessible to the public, journalists, and researchers. This data is crucial for building a platform to display the campaign contributions and expenditures from past years. It will be much simpler to maintain this data in databases, which also makes it possible to obtain findings instantly. Overall, it seems that the data elements in dataset are all related to the complex web of money and politics in the United States. By analyzing data together, it may be possible to gain insights into how money influences elections and which individuals and organizations are most involved in the political process. 

Link: Federal Election Commission Database 

Why Databases and not EXCEL? 

EXCEL: 

Large data is challenging to preserve due of size. Additionally, Excel has difficulty maintaining the large number of entities needed to establish links between the data. 
Data relationships are difficult to maintain and are not possible. 
It will not support the real time data. 
DATABASE: 

When it comes to manipulating and analyzing data, databases are significantly more powerful and adaptable than Excel. Data can be stored in a variety of formats, including text, photos, audio, and video. 
It is flexible for the larger data. 
The relations between the tables are easy. 
The database facilitates real-time applications. 
Using a database, we may conduct more complicated actions like connecting two tables, doing computations, and bulk updating. They also enable increased security and scalability. 
Target User: 

Journalists, Researchers, Advocacy Groups, Voters, and General Public comprise the end users. Journalists often rely on campaign finance data to uncover potential conflicts of interest and to report on the influence of money in politics. They may use the database to search for contributions from specific individuals or organizations, or to compare the fundraising and spending patterns of different candidates. Researchers in political science, economics, and other fields may use the database to analyze the impact of money on political campaigns, to identify trends in campaign finance, or to explore the relationship between political donations and policy outcomes. Advocacy groups may use the database to monitor the fundraising and spending activities of candidates or political parties, to track the activities of specific donors or interest groups, or to identify potential avenues for advocacy and engagement. Voters may use the database to research the candidates running in their districts or states, to learn more about the sources of funding for their campaigns, and to assess the potential influence of money on their elected representatives. The database can be accessed by anyone who is interested in learning more about the role of money in politics during the 2022-2023 election cycle in the United States. The data can be used to increase transparency and accountability in the political process and to inform public discourse on the issue. 

Administrator: Federal Election commission maintains a public database, called the Electronic Filing System (EFS), which contains financial information provided by political committees and candidates. 

Entity Relation Diagram (ERD) 

Before we start designing the E/R diagram, we need to understand the tables in the dataset and their relationships. Based on the table names provided, we can identify the following entities: 

Entities:

Candidates
Committees
Contributors
Campaigns
Contributions
Expenditures
Transactions

Now, let's consider the relationships between these entities. Here are some possible relationships that we can identify based on the dataset: 

Relationships:

Candidates – Committees:- Many to One
CurrentCampaignsForHouseandSenate - Candidates:- One to One
ContributionsByIndividuals - Committees:-  Many to Many
IndependentExpenditures - Candidates:- Many to One
Committees – IndependentExpenditures:- One to Many
Committees – OperatingExpenditures:- One to Many
Committees – PAC&PartySummary:- One to One
Committees – AnyTransactionFromOneCommmitteeToAnother:- Many to Many

Query execution analysis:

Problematic queries were:

1.
SELECT c.CAND_NAME, com.CMTE_NM
FROM candidate c
JOIN candidate_committee cc ON cc.CAND_ID = c.CAND_ID
JOIN committee com ON com.CMTE_ID = cc.CMTE_ID
WHERE cc.CAND_ELECTION_YR = 2020
GROUP BY c.CAND_NAME, com.CMTE_NM
ORDER BY c.CAND_NAME ASC, com.CMTE_NM ASC;

Based on the execution plan,  it seems that the most expensive operation in the query is the sequential scan of the "committee" table, which has a cost of 24.29.

To improve the performance of the query, we can consider creating an index on the "CMTE_ID" column of the "committee" table, which is used in the join condition with the "candidate_committee" table. This will allow for faster retrieval of the relevant rows from the "committee" table.
CREATE INDEX committee_cmte_id_idx ON committee (CMTE_ID); 

2. 
SELECT *
FROM operatingexpenditures
LEFT JOIN contributor
ON operatingexpenditures.CONTRIBUTOR_ID = contributor.CONTRIBUTOR_ID;

Based on the execution pla, it appears that the problematic query involves a left join between the operatingexpenditures and contributor tables on their respective contributor_id columns. The left join operation can be expensive and can consume a lot of resources, especially if the tables are large. 

To optimize this query, consider the following options: 
a. Add an index to the contributor_id column on both tables to speed up the join operation. This can significantly reduce the amount of time required to perform the join by allowing the database to quickly locate matching rows.
b. Rewrite the query to use a more efficient join algorithm. For example, a nested loop join may be more efficient than a hash join or a merge join for small tables.
c. Use a subquery or a common table expression (CTE) to filter the rows before joining. This can reduce the size of the result set and improve the efficiency of the join operation. 

3.
SELECT *
FROM committee
FULL OUTER JOIN candidate_committee
ON committee.CMTE_ID = candidate_committee.CMTE_ID;

Based on the execution plan, it appears that the problematic query involves a full join between the candidate_committee and committee tables on their respective cmte_id columns. The full join operation can be expensive and can consume a lot of resources, especially if the tables are large. 

To optimize this query, consider the following options: 
a. Add an index to the cmte_id column on both tables to speed up the join operation. This can significantly reduce the amount of time required to perform the join by allowing the database to quickly locate matching rows.
b. Rewrite the query to use a more efficient join algorithm. For example, a nested loop join may be more efficient than a hash join or a merge join for small tables.
c. Use a subquery or a common table expression (CTE) to filter the rows before joining. This can reduce the size of the result set and improve the efficiency of the join operation.
