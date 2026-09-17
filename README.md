# Interactive-Dashboard-Development
import streamlit as st
import pandas as pd
import plotly.express as px
import numpy as np

# ============================================================
# PAGE CONFIGURATION
# ============================================================

st.set_page_config(
    page_title="Business Intelligence Dashboard",
    page_icon="📊",
    layout="wide"
)

# ============================================================
# TITLE
# ============================================================

st.title("📊 Interactive Business Intelligence Dashboard")
st.markdown(
    "### Sales, Users, Churn and Category Performance Analysis"
)

st.markdown("---")

# ============================================================
# SIDEBAR
# ============================================================

st.sidebar.header("⚙️ Dashboard Controls")

# ============================================================
# LOAD DATASET
# ============================================================

uploaded_file = st.sidebar.file_uploader(
    "Upload CSV Dataset",
    type=["csv"]
)

# ------------------------------------------------------------
# SAMPLE DATASET
# ------------------------------------------------------------

if uploaded_file is None:

    np.random.seed(10)

    dates = pd.date_range(
        start="2025-01-01",
        end="2025-12-31",
        periods=300
    )

    regions = [
        "North",
        "South",
        "East",
        "West"
    ]

    categories = [
        "Electronics",
        "Clothing",
        "Furniture",
        "Grocery"
    ]

    sample_data = []

    for i in range(300):

        revenue = np.random.randint(
            1000,
            50000
        )

        users = np.random.randint(
            10,
            500
        )

        churn = np.random.choice(
            [0, 1],
            p=[0.85, 0.15]
        )

        ticket_size = np.random.randint(
            500,
            10000
        )

        sample_data.append(
            [
                dates[i],
                np.random.choice(regions),
                np.random.choice(categories),
                revenue,
                users,
                churn,
                ticket_size
            ]
        )

    df = pd.DataFrame(
        sample_data,
        columns=[
            "Date",
            "Region",
            "Category",
            "Revenue",
            "Active_Users",
            "Churn",
            "Ticket_Size"
        ]
    )

    st.sidebar.info(
        "No CSV uploaded. Sample data is being displayed."
    )

else:

    df = pd.read_csv(uploaded_file)

    st.sidebar.success(
        "Dataset uploaded successfully!"
    )

# ============================================================
# CLEAN COLUMN NAMES
# ============================================================

df.columns = (
    df.columns
    .str.strip()
    .str.lower()
    .str.replace(" ", "_")
)

# ============================================================
# FIND IMPORTANT COLUMNS
# ============================================================

def find_column(possible_names):

    for name in possible_names:

        if name in df.columns:
            return name

    return None


date_col = find_column([
    "date",
    "order_date",
    "transaction_date",
    "sale_date"
])

region_col = find_column([
    "region",
    "state",
    "area",
    "location"
])

category_col = find_column([
    "category",
    "product_category",
    "type"
])

revenue_col = find_column([
    "revenue",
    "sales",
    "total_revenue",
    "amount"
])

users_col = find_column([
    "active_users",
    "users",
    "user_count",
    "customers"
])

churn_col = find_column([
    "churn",
    "churned",
    "churn_rate"
])

ticket_col = find_column([
    "ticket_size",
    "avg_ticket_size",
    "average_ticket",
    "order_value"
])

# ============================================================
# CONVERT NUMERIC COLUMNS
# ============================================================

for column in [
    revenue_col,
    users_col,
    churn_col,
    ticket_col
]:

    if column is not None:

        df[column] = pd.to_numeric(
            df[column],
            errors="coerce"
        )

# ============================================================
# CONVERT DATE
# ============================================================

if date_col is not None:

    df[date_col] = pd.to_datetime