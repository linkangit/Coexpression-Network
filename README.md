# RNAseq Co-Expression Network Analysis
![Hub genes](output_0_3.png)
**Hub Gene Analysis**

This repository demonstrates how to build and compare co-expression networks using RNA-seq data. The code processes read-count data (control vs. mutant samples), constructs correlation-based gene networks, computes various network statistics, identifies hub genes, and provides comparisons across conditions.

---

## Table of Contents
1. [Overview](#overview)  
2. [Detailed Steps](#detailed-steps)  
   1. [Sample Information](#sample-information)  
   2. [Loading the Read-Count Table](#loading-the-read-count-table)  
   3. [Filtering and Subsetting](#filtering-and-subsetting)  
   4. [Constructing the Combined Network](#constructing-the-combined-network)  
   5. [Network Statistics and Top Genes](#network-statistics-and-top-genes)  
   6. [Constructing Control vs. Mutant Networks](#constructing-control-vs-mutant-networks)  
   7. [Visualizing and Comparing Networks](#visualizing-and-comparing-networks)  
   8. [Cytoscape Export](#cytoscape-export)  
9. [Usage](#usage)  
10. [Additional Modifications](#additional-modifications)  
11. [Dependencies](#dependencies)  
12. [Contributing](#contributing)  
13. [License](#license)  

---

## Overview

In **RNA-seq** studies, identifying co-expression patterns among genes can provide insights into gene regulatory networks. This code:
1. Reads in a table of raw read-counts.  
2. Defines control and mutant conditions and filters out low-count or irrelevant genes.  
3. Builds correlation-based gene networks for (1) the combined dataset, and (2) separate control vs. mutant datasets.  
4. Computes hub metrics (degree, clustering, betweenness, and eigenvector centralities) to identify influential genes.  
5. Visualizes and compares the networks, including edge weight distributions.  

---

## Detailed Steps

### Sample Information
1. A dictionary (`sample_info_dict`) is created with two columns:  
   - "Sample": The sample IDs (e.g., "Kelley_17", "Kelley_18", etc.)  
   - "Condition": The sample condition (e.g., "C" for control, "mut" for mutant).  
2. This dictionary is converted to a DataFrame called `sample_info`.  
3. Two lists are generated:  
   - `ctrl_list`: Samples that are control.  
   - `variant_list`: Samples that are mutant.  

You can customize these lists to match your own data. For example, if you have different condition labels (like “treated” vs. “untreated”), change "C" to “untreated” and “mut” to “treated”.

### Loading the Read-Count Table
- The script reads a CSV file (`read_counts_table.csv`) in which:  
  - The first column, named `gname`, contains gene IDs or names.  
  - Subsequent columns represent individual samples and their reads.  
- The table is converted so that the gene name (`gname`) is the index, making it easier for subsequent calculations.  

Your CSV file should follow a similar structure:
  
| gname      | Sample1 | Sample2 | Sample3 | ... |
|------------|---------|---------|---------|-----|
| GeneA      | 10      | 15      | 5       | ... |
| GeneB      | 200     | 50      | 70      | ... |

### Filtering and Subsetting
1. The code drops any genes that have a total read count of zero across all samples.  
2. The script subsets the columns to only retain the relevant control and mutant samples (as defined by `ctrl_list` and `variant_list`).  
3. (Optional) The code further subsets the gene list (e.g., first 100 genes) for demonstration and testing. You can remove or change this limit based on the size of your dataset and your computational constraints.

### Constructing the Combined Network
1. The script transposes the **read counts** so that rows are samples and columns are genes.
2. It calculates a **Spearman correlation** matrix among genes.  
3. A network is built by adding edges between gene pairs that have an absolute correlation ≥ 0.7 (by default). This threshold can be adjusted to alter network density.

### Network Statistics and Top Genes
- After constructing the **combined network**, the code calculates:  
  - Degree (number of edges for each gene)  
  - Degree centrality  
  - Clustering coefficient  
  - Betweenness centrality  
  - Eigenvector centrality  
- It prints the top 10 genes in each statistic to easily identify potential “hub” genes.  
- Subgraphs of these “top 10” are also visualized to highlight interconnected genes.

### Constructing Control vs. Mutant Networks
- The script repeats the correlation and network-building process separately for the control-only data (`control_network`) and the mutant-only data (`mutant_network`).  
- This allows direct comparisons of network size, density, average degree, and hub genes.

### Visualizing and Comparing Networks
- The code plots:
  - A **spring-layout** visualization of the combined network (all data).  
  - Side-by-side visualizations of the control and mutant networks, using the same node-size and layout style.  
- Individual histograms compare the **edge weight distributions** (correlation values) between the two networks.

### Cytoscape Export
- For **Cytoscape** visualization, you can export your network to a CSV file that records each edge’s “Source,” “Target,” and “Weight.”  
- Optionally, export node attributes in another CSV file to attach metadata (e.g., degree, clustering) for exploring within Cytoscape.

---

## Usage

1. **Data preparation:**  
   - Format your CSV so that `gname` is the first column, followed by columns named after your samples.  
2. **Sample info:**  
   - Update `sample_info_dict` to match your sample IDs and conditions.  
3. **Parameters to adjust (suggestions):**  
   - **Correlation threshold**: By default 0.7. You can lower or raise this to adjust the sparsity of your network.  
   - **Gene subsetting**: Change or remove the `read_df = read_df.iloc[:100, :]` step if you want to analyze more genes.  
   - **Visualization**: Tweak plot sizes, node color schemes, or layout algorithms for better clarity.  
4. **Run the script** (e.g., in a Jupyter Notebook or Python console).  
5. **Interpret results**. You can look at:
   - The top hub genes (by degree or centrality).  
   - Network-wide metrics like density and average degree.  
   - Differences between the control and mutant networks.  

---

## Additional Modifications

- **Including more conditions**: If you have more than two conditions, expand your filtering logic (e.g., “control,” “mutant1,” “mutant2,” etc.) and build separate or combined networks for each.  
- **Advanced filtering**: Remove genes below a certain expression threshold or filter based on variance or fold-change.  
- **Alternative correlation methods**: The code currently uses Spearman’s method (`.corr(method='spearman')`). You could switch to Pearson (`'pearson'`) depending on your data and assumptions.  
- **Thresholding**: You can implement more nuanced thresholds, such as dynamic correlation cutoffs or adjacency matrices from Weighted Gene Co-expression Network Analysis (WGCNA)-style approaches.  
- **Network analysis**: Add or customize network metrics like closeness centrality, PageRank, or modules detection (communities).  

---

## Dependencies

- Python 3  
- pandas  
- numpy  
- scipy  
- statsmodels (optional, depending on your needs)  
- networkx  
- matplotlib  
- seaborn  

Install any missing packages with:  
pip install pandas numpy scipy statsmodels networkx matplotlib seaborn  

---

## Contributing

Pull requests and suggestions for improvements are highly encouraged. If you encounter a bug or have a question, please open an issue.

---

## License

This project is open-source (MIT License). See the [LICENSE](LICENSE) file for details.

---

**Happy exploring RNA-seq co-expression networks!**
