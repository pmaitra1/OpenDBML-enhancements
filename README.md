# **OpenDBML - A Framework for In-Database Machine Learning**

OpenDBML is a powerful framework for **data transformation, feature engineering, dimensionality reduction, and visualization**. It enables seamless **conversion of datasets** into formats compatible with **libFM, Morpheus, and LMFAO** (Layered Multiple Functional Aggregate Optimization), making it an essential tool for in-database machine learning.

This framework was originally developed by my **supervisor and his PhD student**, and I have contributed additional features to enhance its functionality. Additionally, this repository contains a **dissertation** that discusses the **design, implementation, and potential applications** of OpenDBML in detail. This document provides insights into the theoretical foundation and real-world use cases of the framework.

---

## **Installation**
Ensure you have Python 3.x installed and the necessary dependencies. Clone the repository:

```bash
git clone https://github.com/your-username/opendbml.git
cd opendbml
```

---

## **Loading a Dataset**
To start using OpenDBML, define your dataset’s schema and load it using `CSVConfig`:

```python
from polyrecsys import configs

data_dir = 'path/to/your/dataset'

ratings_schema = {'user_id': str, 'movie_id': str, 'rating': int}
users_schema = {'user_id': str, 'gender': str, 'age': str, 'occupation': str}
movies_schema = {'movie_id': str, 'title': str, 'genres': {'movie_id': str, 'genres': str}}

ratings_config = configs.CSVConfig(f'{data_dir}/ratings.dat', name='ratings', schema=ratings_schema, pk=['user_id', 'movie_id'])
users_config = configs.CSVConfig(f'{data_dir}/users.dat', name='users', schema=users_schema, pk=['user_id'])
movies_config = configs.CSVConfig(f'{data_dir}/movies.dat', name='movies', schema=movies_schema, pk=['movie_id'])

dataset_config = configs.DatasetConfig(ratings_config, users_config, movies_config)
dataset = configs.PolyDataset(dataset_config, lazy=False, name="MyDataset")
print("Dataset loaded successfully.")
```

---

## **Declaring a Join Order**
A **join order** refers to a **nested dictionary structure** used to represent a join tree of a dataset. Each key in the dictionary is a tuple consisting of the **name, foreign key, and primary key** of a relation, and the value is another nested join order or `None` indicating a leaf node.

Example:

```python
join_order = {
    ('ratings', None, None): {
        ('users', 'user_id', 'user_id'): None,
        ('movies', 'movie_id', 'movie_id'): {
            ('genres', 'movie_id', 'movie_id'): None
        }
    }
}
```

This structure defines how the dataset’s tables are joined for feature engineering and machine learning tasks.

---

## **Modifying Attributes**
Use the `modify_attributes()` function to apply transformations to features. Example: log transformation for `age`:

```python
from polyrecsys import transform

dataset.modify_attributes([('users', 'age', transform.log_transform)], update_schema=True)
print("Attributes modified successfully.")
```

---

## **Getting Initial Statistics**
Retrieve dataset statistics using `get_statistics()`:

```python
dataset.get_statistics(join_order, features=['age', 'rating'])
```

---

## **Reducing Dimensionality**
Apply **Principal Component Analysis (PCA)** for dimensionality reduction:

```python
dataset.pca(join_order=join_order, n_components=2, plot=True)
```

---

## **Generating Exploratory Plots**
Create visualizations to explore feature distributions:

```python
dataset.plot(join_order=join_order, features=['rating', 'gender'])
```

---

## **Converting Data for Machine Learning Frameworks**
OpenDBML supports data conversion for three in-database machine learning frameworks:

### **Convert to libFM Format**
```python
dataset.to_svml(out_dir='output/libfm', target='rating', join_order=join_order)
```

### **Convert to Morpheus Format**
```python
dataset.to_morpheus(out_dir='output/morpheus', target='rating', join_order=join_order)
```

### **Convert to LMFAO Format**
```python
dataset.to_lmfao(out_dir='output/lmfao', target='rating', join_order=join_order)
```

---

## **Example Workflow**
A complete pipeline using OpenDBML:

```python
from polyrecsys import configs, transform

data_dir = 'path/to/data'
dataset = configs.PolyDataset.load_from_directory(data_dir)

dataset.modify_attributes([('users', 'age', transform.log_transform)], update_schema=True)
dataset.get_statistics(join_order)
dataset.pca(join_order=join_order, n_components=2, plot=True)
dataset.plot(join_order=join_order, features=['rating', 'age'])

dataset.to_svml(out_dir='output/libfm', target='rating', join_order=join_order)
dataset.to_morpheus(out_dir='output/morpheus', target='rating', join_order=join_order)
dataset.to_lmfao(out_dir='output/lmfao', target='rating', join_order=join_order)
```

---

## **Summary of OpenDBML Functions**

| Function | Description |
|----------|-------------|
| `modify_attributes(attribute_list, update_schema=True)` | Modify dataset features (e.g., log transformation) |
| `get_statistics(join_order, features)` | Retrieve dataset statistics |
| `pca(join_order, n_components=2, plot=True)` | Perform PCA for dimensionality reduction |
| `plot(join_order, features)` | Generate exploratory plots |
| `to_svml(out_dir, target, join_order)` | Convert dataset to **libFM** format |
| `to_morpheus(out_dir, target, join_order)` | Convert dataset to **Morpheus** format |
| `to_lmfao(out_dir, target, join_order)` | Convert dataset to **LMFAO** format |

---

## **Contributing**
Contributions are welcome! Submit a **pull request** or open an **issue**.

## **License**
All rights reserved. This project is private and proprietary.
Unauthorized copying, distribution, or modification is prohibited.
For access, contact praptimaitra@gmail.com.

---

