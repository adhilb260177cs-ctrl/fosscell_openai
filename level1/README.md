# DataProcessor

A Python module for numerical dataset ingestion, statistical analysis, range filtering, affine transformations, and persistent CSV storage.

---

## 1. Overview

`DataProcessor` provides an end-to-end interface to manage and compute metrics on one-dimensional numerical datasets. It addresses common data manipulation needs by offering:
- **Numerical Ingestion:** Append single numbers or batch arrays with strict type validation.
- **Descriptive Statistics:** Calculate mean, median, sample variance, and sample standard deviation.
- **Data Filtering & Transformation:** Apply inclusive numerical bounds and affine linear transformations ($y = \text{factor} \cdot x + \text{offset}$).
- **CSV Storage:** Export stored numbers to disk and reload previously saved datasets.

---

## 2. Requirements & Setup

### Requirements
- **Python Version:** Python 3.7+
- **Standard Library Dependencies:** `math`, `csv`, `typing`

### Setup & Import
No third-party packages are required. Place `data_processor.py` in your working directory and import the class:

```python
from data_processor import DataProcessor
3. API Reference
__init__(self, initial_data: Optional[List[Union[int, float]]] = None) -> None
Initializes a new DataProcessor instance with an optional starting sequence of numbers.

Parameters:

initial_data (Optional[List[Union[int, float]]]): Optional initial list of numeric values. Default is None.

Raises:

TypeError: If initial_data contains non-numeric elements or is an unsupported data type.

add_data(self, item: Union[int, float]) -> None
Appends a single number to the dataset.

Parameters:

item (Union[int, float]): An integer or float to add.

Returns: None

Raises:

TypeError: If item is not an int or float (boolean values are rejected).

batch_add(self, items: List[Union[int, float]]) -> None
Appends an iterable collection of numbers to the dataset.

Parameters:

items (List[Union[int, float]]): An iterable containing integers or floats.

Returns: None

Raises:

TypeError: If items is not iterable or any element within it is non-numeric.

calculate_mean(self) -> float
Computes the arithmetic mean of the dataset.

Returns: float – The calculated mean.

Raises:

ValueError: If the dataset is empty.

calculate_median(self) -> float
Computes the median of the dataset.

Returns: float – The median value (averages the two middle values if the dataset count is even).

Raises:

ValueError: If the dataset is empty.

calculate_variance(self) -> float
Computes the sample variance (
N−1 degrees of freedom).

Returns: float – The sample variance.

Raises:

ValueError: If the dataset contains fewer than 2 elements.

calculate_std_dev(self) -> float
Computes the sample standard deviation.

Returns: float – The sample standard deviation (
variance

​
 ).

Raises:

ValueError: If the dataset contains fewer than 2 elements.

get_summary_statistics(self) -> Dict[str, Union[int, float]]
Generates a consolidated dictionary containing key descriptive statistics.

Returns: Dict[str, Union[int, float]] with the following structure:

"count": Number of data points (int)

"mean": Arithmetic mean (float)

"median": Median value (float)

"variance": Sample variance (float)

"std_dev": Sample standard deviation (float)

"min": Minimum value (Union[int, float])

"max": Maximum value (Union[int, float])

Raises:

ValueError: If the dataset contains fewer than 2 elements.

filter_data(self, min_val: Optional[Union[int, float]] = None, max_val: Optional[Union[int, float]] = None) -> List[Union[int, float]]
Returns a new filtered list containing values within the inclusive interval [min_val, max_val].

Parameters:

min_val (Optional[Union[int, float]]): Minimum threshold value (inclusive). Default is None (unbounded lower limit).

max_val (Optional[Union[int, float]]): Maximum threshold value (inclusive). Default is None (unbounded upper limit).

Returns: List[Union[int, float]] – Filtered list of values.

Raises:

TypeError: If provided thresholds are non-numeric.

ValueError: If min_val is strictly greater than max_val.

transform_data(self, factor: Union[int, float] = 1.0, offset: Union[int, float] = 0.0) -> List[float]
Applies an affine transformation 
y=x⋅factor+offset to all items and returns the transformed values.

Parameters:

factor (Union[int, float]): Multiplier constant. Default is 1.0.

offset (Union[int, float]): Additive constant. Default is 0.0.

Returns: List[float] – Transformed numeric values.

Raises:

TypeError: If factor or offset are not numeric.

export_to_csv(self, filepath: str) -> None
Exports all numeric entries from the dataset to a single-column CSV file on disk.

Parameters:

filepath (str): Target file destination path.

Returns: None

Raises:

OSError: If the target path cannot be created or written to.

load_from_csv(self, filepath: str) -> None
Reads a single-column CSV file from disk and replaces the current dataset with its contents.

Parameters:

filepath (str): Source CSV file path.

Returns: None

Raises:

FileNotFoundError: If the file does not exist.

ValueError: If a record in the CSV cannot be parsed as a float.

4. Code Examples
Basic Usage & Statistical Analysis
Python
from data_processor import DataProcessor

# Initialize processor with starter values
dp = DataProcessor([10, 20, 30])

# Add single and multiple numbers
dp.add_data(40)
dp.batch_add([50, 60, 70])

# Retrieve summary statistics
stats = dp.get_summary_statistics()
print("Summary Statistics:", stats)
# Output:
# {'count': 7, 'mean': 40.0, 'median': 40.0, 'variance': 466.67, 'std_dev': 21.60, 'min': 10, 'max': 70}
Filtering & Affine Transformations
Python
from data_processor import DataProcessor

dp = DataProcessor([5, 12, 19, 24, 31, 45])

# Filter elements between 10 and 30 inclusive
filtered = dp.filter_data(min_val=10, max_val=30)
print("Filtered Data:", filtered)
# Output: [12, 19, 24]

# Scale elements by 2 and add 5: y = 2*x + 5
transformed = dp.transform_data(factor=2.0, offset=5.0)
print("Transformed Data:", transformed)
# Output: [15.0, 29.0, 43.0, 53.0, 67.0, 95.0]
CSV File Export and Import
Python
from data_processor import DataProcessor

# Write current dataset to CSV
dp = DataProcessor([1.5, 2.75, 3.125, 4.0])
dp.export_to_csv("measurements.csv")

# Load data into a new instance
new_dp = DataProcessor()
new_dp.load_from_csv("measurements.csv")

print("Mean of loaded data:", new_dp.calculate_mean())
# Output: 2.84375
5. Error Handling & Edge Cases
Scenario	Methods Involved	Behavior
Empty Dataset	calculate_mean, calculate_median	Raises ValueError ("Cannot calculate metrics on an empty dataset.")
Small Sample Size (N<2)	calculate_variance, calculate_std_dev, get_summary_statistics	Raises ValueError ("Sample statistics require at least 2 observations.")
Non-Numeric Inputs	add_data, batch_add, __init__	Validates types against (int, float) excluding bool. Raises TypeError on invalid items.
Inverted Thresholds	filter_data	Raises ValueError if min_val > max_val.
Missing Input File	load_from_csv	Raises FileNotFoundError if the path does not exist.
Invalid CSV Values	load_from_csv	Raises ValueError when encountering non-numeric rows or corrupted data.
Write Permission Errors	export_to_csv	Propagates PermissionError or OSError if the path is inaccessible.
