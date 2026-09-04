# Data Catalog Repository Layout (Multi-Project)

Since you have many projects and need a scalable way to find and keep track of your datasets—including their **source, size, type of data, and where to get them**—a central Data Catalog repository is the perfect solution.

Here is an updated structure and strategy that focuses on making your datasets searchable and easily usable across all your different projects.

## 1. Directory Structure

This structure uses a central registry approach. Datasets aren't hidden inside individual project folders; instead, there is a central pool of data that any project can reference.

```text
data-catalog/
├── 📂 registry/                # The "brain" of your catalog - where you track everything
│   ├── 📄 datasets.yaml        # Master list of all datasets (source, size, type)
│   ├── 📄 projects.yaml        # List of your projects and which datasets they use
│   └── 📂 schemas/             # Data dictionaries for specific complex datasets
│       ├── 📄 nutrition_api_v1.md
│       └── 📄 fda_food_db.md
│
├── 📂 data/                    # The actual data files (or symlinks to them)
│   ├── 📂 raw/                 # Unchanged source data downloaded from external APIs/sites
│   ├── 📂 processed/           # Cleaned data ready for your projects to consume
│   └── 📄 .gitignore           # Important: Ignore large files so Git doesn't crash!
│
├── 📂 scripts/                 # Utilities to manage the catalog
│   ├── 📄 download_source.py   # Script to pull data from a URL if it's missing
│   └── 📄 update_registry.py   # Script to auto-calculate file sizes and update datasets.yaml
│
└── 📄 README.md                # Quickstart on how to find data and add new data
```

## 2. Tracking Datasets (The Registry)

To solve your need to track **where to get them, how big they are, and what kind of data they contain**, you should use a YAML or JSON file as a master registry (`registry/datasets.yaml`). 

This acts as a lightweight database that is easy for humans to read and scripts to parse.

### Example `registry/datasets.yaml`

```yaml
datasets:
  - id: usda_food_data_central
    name: USDA FoodData Central
    description: Comprehensive nutritional information for generic and branded foods.
    source_url: https://fdc.nal.usda.gov/download-datasets.html
    local_path: data/raw/usda_food_data_central/
    format: CSV
    size: 2.4 GB
    update_frequency: Monthly
    projects_using_this: 
      - mealcoachai
      - recipe_generator
    tags: [nutrition, public, usda]

  - id: rn_app_user_behavior
    name: MealCoach App User Behavior (Anonymized)
    description: Clicks, screen time, and feature usage from the React Native App.
    source_url: internal (Google Cloud Storage bucket `rn-logs-prod`)
    local_path: data/raw/rn_app_logs/
    format: JSONL (JSON Lines)
    size: 450 MB
    update_frequency: Daily
    projects_using_this:
      - user_retention_model
    tags: [analytics, internal, user_behavior]
```

## 3. How to Scale This Across Projects

1. **Keep the Catalog Separate**: Keep this `data-catalog` in its own Git repository.
2. **Reference, Don't Copy**: When working in a specific project (like `mealcoachai`), don't copy the 2.4GB USDA dataset into the project folder. Instead, either:
   * Read the data directly from the absolute path of the `data-catalog/data/` folder on your machine.
   * Write a small Python script in your project that reads `data-catalog/registry/datasets.yaml` to dynamically find where the data is located.
3. **Automate**: As the catalog grows, you can write a short Python script (`scripts/update_registry.py`) that scans your `data/` folder, calculates the exact file sizes, and automatically updates the `size` field in your `datasets.yaml`.

This approach ensures that if multiple projects need the same nutritional database, you only download and store it once, and anyone can instantly see where it came from.
