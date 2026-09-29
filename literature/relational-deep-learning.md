# Relational Deep Learning

## Paper

**Position: Relational Deep Learning - Graph Representation Learning on Relational Databases**

Matthias Fey, Weihua Hu, Kexin Huang, Jan Eric Lenssen, Rishabh Ranjan, Joshua Robinson, Rex Ying, Jiaxuan You, and Jure Leskovec (2024).

Published at ICML 2024.

---

## Problem

A lot of real-world data is stored in **relational databases**.

Instead of having everything in one table, the information is spread across multiple tables connected by relationships.

For example, an online store might have:

- a `Customers` table
- a `Products` table
- a `Transactions` table

A transaction can be connected to both a customer and a product through primary and foreign keys.

The problem is that most machine learning models are designed to work with a single table or a fixed feature matrix. To use traditional ML methods on a relational database, we often have to manually join tables and create features.

This process is called **feature engineering**.

The authors argue that this can be time-consuming and can lose some of the relational structure contained in the original database.

The main question is therefore:

> **Can we train deep learning models directly on relational databases without manually flattening the data into one table?**

---

## Main Idea

The main idea of **Relational Deep Learning (RDL)** is to represent a relational database as a **graph**.

Instead of thinking of the database only as tables:

```text
Customers
Products
Transactions
```

the authors convert it into a graph:

```text
Customer ─── Transaction ─── Product
     │
     └──── other related entities
```

More precisely, each **row becomes a node**, and primary-key/foreign-key relationships become **edges**.

The resulting graph is both:

- **heterogeneous** — different types of entities and relationships exist
- **temporal** — the data can change over time

A Graph Neural Network can then perform message passing over this graph and learn representations using information from related tables.

The important part is that the model can learn from the relational structure **without manually creating all the cross-table features first**.

---

## Method

The framework can be understood in three main steps.

### 1. Start with a relational database

Suppose we have:

```text
Customers
    ↓
Transactions
    ↓
Products
```

The tables contain different types of information.

The relationships between tables are represented by primary and foreign keys.

### 2. Convert the database into a relational entity graph

Each row becomes a node.

For example:

```text
Customer 1
Customer 2
Product A
Product B
Transaction 100
Transaction 101
```

Relationships between rows become edges.

So instead of treating the database as independent tables, the model can see the structure connecting the entities.

### 3. Apply a Graph Neural Network

The GNN performs **message passing**.

A node can receive information from connected nodes and use that information to build a representation.

For example, to predict something about a customer, the model might use information from:

```text
Customer
   ↓
Transactions
   ↓
Products
```

The model can therefore use information that is spread across several tables.

The paper presents this as an **end-to-end learning approach**, meaning the representation learning and prediction are handled by the model rather than requiring manual feature engineering first.

---

## Why This Is Interesting

The key thing I took from this paper is that **relationships themselves can be useful information**.

In a normal tabular ML setup, I might turn everything into columns:

```text
customer_age
number_of_purchases
average_purchase_price
...
```

But when doing this manually, I have to decide which relationships and aggregations are useful.

RDL tries to let the model learn from the underlying structure instead.

This makes the problem closer to graph learning:

> **Instead of only asking what features an entity has, the model can also learn from which other entities it is connected to.**

---

## RelBench

The paper also introduces **RelBench**, a benchmark and implementation for relational deep learning.

The purpose is to make it easier to evaluate models on relational databases using standardized datasets and predictive tasks.

This is important because a new research area needs a way to compare different methods under common evaluation settings.

RelBench was released as an open benchmark for predictive machine learning on relational databases.

---

## Main Findings

The authors argue that relational databases can be treated naturally as temporal, heterogeneous graphs and that Graph Neural Networks can learn useful representations directly from this structure.

Their experiments show that relational deep learning can produce strong predictive performance while avoiding the need for manually engineered features across tables.

The paper's broader contribution is to propose **Relational Deep Learning as a research direction**, rather than presenting only one new model.

The authors describe this as a generalization of graph machine learning to relational databases.

---

## Limitations / Open Problems

This paper is partly a **position paper**, so it also identifies challenges rather than claiming that relational deep learning is a solved problem.

Some important challenges include:

### 1. Large databases

Real databases can contain huge numbers of rows and relationships.

Building and training GNNs over these graphs efficiently is difficult.

### 2. Temporal information

Relational data often changes over time.

A model needs to avoid using information that would not have been available at the prediction time.

This makes temporal splits and evaluation important.

### 3. Heterogeneous structure

Different tables represent different kinds of entities, and different relationships connect them.

The model therefore needs to handle multiple node and edge types.

### 4. Generalization

A useful relational model should ideally work across different databases and tasks rather than being heavily customized for one database.

The authors identify these issues as important directions for future work.

---

## Connection to My Previous Papers

This paper is **not directly part of the same mechanistic interpretability problem** as Wang et al. and Conmy et al.

The connection is more about how I became interested in **structure inside machine learning systems**.

### Wang et al. (2022)

Wang et al. study the internal structure of GPT-2 and identify a circuit responsible for IOI.

Their question is roughly:

> How does information flow through a neural network to produce a particular behavior?

### Conmy et al. (2023)

Conmy et al. ask whether the process of discovering such circuits can be partially automated.

Their question is roughly:

> Can we automatically identify important connections in a neural network?

### Relational Deep Learning

RDL looks at structure from a different direction.

Instead of studying the internal computational structure of a trained transformer, it starts with **structured relational data** and asks:

> How can a neural network learn effectively from the relationships between entities?

So I see this paper as a **related research direction**, rather than a direct continuation of the IOI literature.

---

## My Understanding

My understanding is that the main problem is actually quite simple:

**Real-world data is often connected, but many machine learning pipelines treat it as if it were just one flat table.**

For example:

```text
Customer
   ↓
Transaction
   ↓
Product
```

contains useful information about relationships.

Traditional machine learning might turn this into a single table by manually joining everything and creating features.

RDL instead says:

> **Why not represent the relationships directly and let a graph neural network learn from them?**

So the basic idea I understand is:

```text
Relational Database
        ↓
Convert rows into nodes
        ↓
Convert relationships into edges
        ↓
Relational Entity Graph
        ↓
Graph Neural Network
        ↓
Learn representations
        ↓
Prediction
```

The part I find most interesting is that the **structure of the data becomes part of what the model learns from**.

This also helped me see a difference between my first two papers and this one.

In Wang et al., I was studying the internal structure of a trained model.

In Conmy et al., I was studying how we might automatically discover that internal structure.

In RDL, I am looking at a different kind of structure: **the relationships already present in the data.**

I am still learning how Graph Neural Networks perform message passing and how temporal and heterogeneous graphs are handled in practice. I would like to understand these parts better before making a stronger connection between relational learning and my mechanistic interpretability work.

---

## Questions I Still Have

- How exactly does message passing work when there are many different types of tables and relationships?
- How does RDL prevent information from the future from leaking into a prediction?
- How are very large relational graphs sampled efficiently?
- How much does the relational structure contribute compared with ordinary tabular features?
- Can similar ideas about discovering structure in relational data help us understand structure inside neural networks?

These are questions I would like to explore further rather than treating the connection as established.
