### Goal and Audience

When creating effective data visualizations, it's crucial to consider:

- **Goal**: What message are you trying to convey?
- **Audience**: Who are you presenting this data to?

---

### Visual Cues for Comparison

Different types of plots can affect how easily viewers can extract information. Consider the following:

- **Pie Plots**: Represent quantities using both areas and angles. However, humans aren't great at precisely quantifying angles or areas.
- **Donut Plots**: Only use area, making comparisons even harder.
- **Bar Plots**: Use bar lengths proportional to the quantities of interest. Humans are much better at judging linear measures, making bar plots more effective for comparisons.

> ✅ *If you must use a pie chart, label each slice with its percentage to avoid inference.*

---

### Include Zero When Appropriate
- When using **bar plots**, always start the bars at `0`. Omitting 0 can exaggerate small differences, misleading viewers.
- This is because bar plots imply that the length is proportional to the quantities being displayed.
- When using **position** (e.g., scatter plots), including 0 is not always necessary — especially when comparing differences between groups relative to within-group variability.

---

### Avoid Distorting Quantities

Ensure your visualizations **accurately represent the data** without distortion.

> For example, `ggplot2` defaults to using area rather than radius.

---

### Meaningful Order

Order data in a meaningful way:

- Avoid alphabetical order unless it serves a specific purpose.
- Instead, order by a relevant quantity to highlight key insights (e.g., ordering states by danger level).

---

### Show Data
- Present the data clearly and directly.
- Aligning data horizontally or vertically can aid comparison.
- Use the same range on the x-axis for better comparison.

---

### Transformations

Transformations can **reveal hidden patterns**:

- A **log transformation** can make data more visible, especially with skewed distributions.

---

### Color Considerations

Use color to enhance visualizations, but:

- Be mindful of **color blindness**.
- Choose a **color blind friendly palette** to ensure accessibility.

---

### Encoding a 3rd Variable

Consider how to **effectively represent additional variables** in your plots without cluttering the visualization.

---

### Avoiding Pseudo 3D Plots

Avoid using **pseudo 3D plots**, as they:

- Rarely provide additional insight
- Often **distort the data**

---

### Avoiding Too Many Significant Digits

Simplify your data labels:

- Avoid too many significant digits.
- Place values being compared on **columns rather than rows**.

---

## Model Deployment using Flask 🚀

### Web Applications

> A web application (or web app) is application software that runs on a web server, unlike computer-based software programs that are run locally on the operating system (OS) of the device.

- Web applications are accessed through a browser with an active network connection.  

> — *Wikipedia*

---

### Web Application Frameworks

**Web Application Framework** or **Web Framework**:

> A collection of modules/libraries that helps the developer to write applications.

Frameworks eliminate the need to write low-level code, such as protocols or thread management.

---

### Flask

- Flask is a **micro-framework** for web application development in Python with minimal external dependencies.
- **License**: BSD
- Flask is a wrapper around:
    - **Werkzeug**: A collection of libraries for creating WSGI-compatible web applications in Python.
    - **Jinja2**: A template engine for Python used when rendering data to web pages.

---

### Machine Learning Model Deployment

> **Model deployment** is the engineering task of exposing an ML model to real-world use, often via real-time APIs.

Think of model deployment more broadly as **online inference in the cloud**.

---

### Practical Implementation

1. Train a machine learning model using `sklearn` in Python.
2. Deploy the trained model to a server using `Flask` in Python.

---

### Prerequisites

1. Create an account at [ngrok](https://ngrok.com/) and obtain your authentication key from the dashboard.
2. Download and extract the test data from [this link](https://drive.google.com/file/d/1XhtEyisWJT4I64dzWsnxy6HVqjOsf5Xk/view?usp=share_link).

---

### Code Reference

🔗 [Flask Example on GitHub](https://github.com/debanga/flask_example)
