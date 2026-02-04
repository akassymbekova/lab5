# LAB 5 — Mini-MapReduce on Amazon EMR

## Overview

This project runs a **WordCount MapReduce** job on an Amazon EMR (managed Hadoop) cluster using Hadoop Streaming with Python mapper and reducer scripts. The dataset is stored in HDFS and the output is written back to HDFS.

---

## Tech Stack

- **Amazon EMR** (Hadoop)
- **HDFS** (Hadoop Distributed File System)
- **YARN / MapReduce**
- **Python** (Hadoop Streaming)

---

## Dataset

**Simple English Wikipedia sample dump:**  
https://github.com/LGDoor/Dump-of-Simple-English-Wiki

### Download on the EMR master node:

```bash
wget https://github.com/LGDoor/Dump-of-Simple-English-Wiki/raw/refs/heads/master/corpus.tgz
ls -lh corpus.tgz
tar -xvzf corpus.tgz
```

---

## HDFS Setup

### Create HDFS input folder and upload the dataset:

```bash
hdfs dfs -mkdir -p /user/hadoop/input
hdfs dfs -put -f corpus.txt /user/hadoop/input/
hdfs dfs -ls /user/hadoop/input/
```

### (Optional) Create output folder:

```bash
hdfs dfs -mkdir -p /user/hadoop/output
```

---

## Run WordCount (Hadoop Streaming)

> **IMPORTANT:** Hadoop output directory must not already exist.

### Remove previous output if needed:

```bash
hdfs dfs -rm -r -f /user/hadoop/output
```

### Run the job:

```bash
hadoop jar /usr/lib/hadoop-mapreduce/hadoop-streaming.jar \
  -input /user/hadoop/input/ \
  -output /user/hadoop/output/ \
  -mapper mapper.py \
  -reducer reducer.py \
  -file mapper.py \
  -file reducer.py
```

---

## Validate Output

### List output files:

```bash
hdfs dfs -ls /user/hadoop/output/
```

### Preview results:

```bash
hdfs dfs -head /user/hadoop/output/part-00000
```

---

## Experiment (Scenario A — Scaling)

The job was executed with different cluster configurations to measure performance:

| Configuration | Runtime |
|--------------|---------|
| **2 Core nodes** | ~65 seconds |
| **4 Core nodes** | ~59 seconds |

> **Note:** Runtime was measured using wall-clock timestamps (`date` command) before and after job execution.

---

## Project Structure

```
.
├── README.md
├── mapper.py          # Python mapper script
├── reducer.py         # Python reducer script
└── corpus.txt         # Dataset (after extraction)
```

---
