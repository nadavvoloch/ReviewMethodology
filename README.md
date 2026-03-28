# ReviewMethodology
The csv files are a complete DB for Neo4J. Here are the necessary Cypher commands for operating it:
// Load Papers.csv and handle missing DOI and URL values LOAD CSV WITH HEADERS FROM 'file:///Papers.csv' AS row MERGE (p:Paper {id: toInteger(row.ID), title: row.Title, authors: row.Authors, year: toInteger(row.Year), platform: row.Publication platform}) SET p.doi = CASE WHEN row.DOI IS NULL OR row.DOI = '' THEN NULL ELSE row.DOI END SET p.url = CASE WHEN row.URL IS NULL OR row.URL = '' THEN NULL ELSE row.URL END;

// STEP 1 // Load Domains.csv LOAD CSV WITH HEADERS FROM 'file:///Domains.csv' AS row MERGE (d:Domain {id: toInteger(row.ID), description: row.description, review_url: row.review_url});

// Load PapersDomains.csv LOAD CSV WITH HEADERS FROM 'file:///PapersDomains.csv' AS row MATCH (p:Paper {id: toInteger(row.Paper ID)}) MATCH (d:Domain {id: toInteger(row.Domain ID)}) MERGE (p)-[:BELONGS_TO]->(d); // STEP 1 // Load Actions.csv LOAD CSV WITH HEADERS FROM 'file:///Actions.csv' AS row MERGE (d:Action {id: toInteger(row.ID), description: row.description});

// Load PapersActions.csv LOAD CSV WITH HEADERS FROM 'file:///PapersActions.csv' AS row MATCH (p:Paper {id: toInteger(row.Paper ID)}) MATCH (d:Action {id: toInteger(row.Action ID)}) MERGE (p)-[:BELONGS_TO]->(d);

// STEP 1 // Load Approach.csv LOAD CSV WITH HEADERS FROM 'file:///Approach.csv' AS row MERGE (d:Approach {id: toInteger(row.ID), description: row.description});

// Load PapersApproaches.csv LOAD CSV WITH HEADERS FROM 'file:///PapersApproaches.csv' AS row MATCH (p:Paper {id: toInteger(row.Paper ID)}) MATCH (d:Approach {id: toInteger(row.Approach ID)}) MERGE (p)-[:BELONGS_TO]->(d);

// STEP 1 // Load RelationDirection.csv LOAD CSV WITH HEADERS FROM 'file:///RelationDirection.csv' AS row MERGE (d:Relation {id: toInteger(row.ID), description: row.description});

// Load PapersRelations.csv LOAD CSV WITH HEADERS FROM 'file:///PapersRelationDirection.csv' AS row MATCH (p:Paper {id: toInteger(row.Paper ID)}) MATCH (d: Relation {id: toInteger(row.Relation ID)}) MERGE (p)-[:BELONGS_TO]->(d);

// STEP 1: Import excluded papers as nodes

LOAD CSV WITH HEADERS FROM 'file:///ExcludedPapers.csv' AS row
MERGE (e:ExcludedPaper {id: toInteger(row.ID)})
SET
e.title = row.Title,
e.authors = row.Authors,
e.year = CASE WHEN row.Year IS NULL OR row.Year = '' THEN NULL ELSE toInteger(row.Year) END,
e.platform = row.`Publication platform`,
e.doi = CASE WHEN row.DOI IS NULL OR row.DOI = '' THEN NULL ELSE row.DOI END,
e.url = CASE WHEN row.URL IS NULL OR row.URL = '' THEN NULL ELSE row.URL END,
e.exclusionReason = row.`reason for exclusion`;

// STEP 2 // Count the number of papers per action MATCH (p:Paper)-[:BELONGS_TO]->(d:Action) RETURN d.description AS Action, COUNT(p) AS PaperCount ORDER BY PaperCount DESC; // STEP 2 // Count the number of papers per domain MATCH (p:Paper)-[:BELONGS_TO]->(d:Domain) RETURN d.description AS Domain, COUNT(p) AS PaperCount ORDER BY PaperCount DESC; // STEP 2 // Count the number of papers per approach MATCH (p:Paper)-[:BELONGS_TO]->(d:Approach) RETURN d.description AS Approach, COUNT(p) AS PaperCount ORDER BY PaperCount DESC;

// STEP 2 // Count the number of papers per relation direction MATCH (p:Paper)-[:BELONGS_TO]->(d: Relation) RETURN d.description AS Relation, COUNT(p) AS PaperCount ORDER BY PaperCount DESC;
