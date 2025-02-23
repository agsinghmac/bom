#Data set to create Parts and EBOM in neo4j database instance

##Here are the dataset files
  - data/parts.csv
  - data/ebom_relationships.csv
  - data/revised_to_relationships.csv

##Use below scripts to load the data in neo4j

###Load Parts

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/agsinghmac/bom/refs/heads/main/data/parts.csv' AS row
CREATE (:Part {
  name: toInteger(row.name),
  type: row.type,
  revision: row.revision,
  description: row.description,
  unit_of_measure: row.unit_of_measure,
  length: toFloat(row.length),
  height: toFloat(row.height),
  breadth: toFloat(row.breadth),
  weight: toFloat(row.weight),
  cost: toFloat(row.cost),
  rohs: toBoolean(row.rohs),
  weee: toBoolean(row.weee),
  material: row.material,
  status: row.status,
  reason_for_change: row.reason_for_change
});
CREATE INDEX FOR (p:Part) ON (p.name);

##Load EBOM relationship

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/agsinghmac/bom/refs/heads/main/data/ebom_relationships.csv' AS row
MATCH (parent:Part {name: toInteger(row.parent_name)})
MATCH (child:Part {name: toInteger(row.child_name)})
CREATE (parent)-[:EBOM {
  quantity: toInteger(row.quantity),
  find_number: row.find_number
}]->(child);

##Load Revision chain

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/agsinghmac/bom/refs/heads/main/data/revised_to_relationships.csv' AS row
MATCH (older:Part {name: toInteger(row.older_name), revision: row.older_revision})
MATCH (newer:Part {name: toInteger(row.newer_name), revision: row.newer_revision})
CREATE (older)-[:REVISED_TO]->(newer);
