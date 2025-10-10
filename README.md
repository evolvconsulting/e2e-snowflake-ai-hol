# evolv End-to-end Snowflake AI Hands on Lab

## Overview

This HOL (hands-on lab) explores how to build a complete AI-powered solution on Snowflake that automates and optimizes sales performance, marketing campaigns, and revenue attribution while enhancing the customer experience with AI-driven insights.

You will work with Snowflake’s newest AI capabilities - Cortex Search, Analyst, Agents & Snowflake Intelligence - with a demonstration of Openflow for unstructured data ingestion.  By completing this lab, you will learn how to AI-ready your data and unlock insights & value through modern agentic analytics.
Participants will build an end-to-end AI-powered solution that:
- Prepares unstructured data for AI retrieval
- Enriches structured data with semantic views for AI understanding
- Delivers insights through Snowflake Intelligence

By completing this HOL, you will learn how to:
- Build semantic views for faster, business-friendly insights
- Use Snowflake Intelligence to query and summarize data natively in Snowflake
- Unify data, AI, and productivity into one streamlined platform

By the end, teams will understand how to embed AI in Snowflake to accelerate insights, unlock new data products, and boost productivity.

## Prerequisites

1. A [free Snowflake 30-day trial environment](https://signup.snowflake.com/) to follow along with this lab.
2. Basic knowledge of SQL, database concepts, and objects.
3. Familiarity with CSV (comma-delimited) files.

## Setup

### Prepare Your Lab Environment
If you haven't already, register for a [Snowflake free 30-day trial](https://signup.snowflake.com/). This lab assumes you are using a new Snowflake account created by registering for a trial and selecting the AWS cloud provider and a US region.

The Snowflake edition (Standard, Enterprise, Business Critical, etc.) you use for this lab, does not matter.

After registering, you will receive an email with an activation link and URL for accessing your Snowflake account.

#### Logging into the Snowflake User Interface (UI)
Open a browser window and enter the URL of your Snowflake 30-day trial environment that was sent with your registration email. Enter the username and password that you specified during the registration.

#### Create Workspace

Create a Workspace connected to this Git repository as follows:
1. In the left navigation, select **Projects » Workspaces**.
2. Select **+ Add new**, select **SQL File**, and name it to  your liking.
3. Paste in the following statements and select **Runn all** or *Ctrl + Shift + Enter*.
    ```sql
    USE ROLE ACCOUNTADMIN;

    -- Create API Integration for GitHub (public repository access)
    CREATE OR REPLACE API INTEGRATION EVOLV_GITHUB_API_INTEGRATION
        API_PROVIDER = git_https_api
        API_ALLOWED_PREFIXES = ('https://github.com/evolvconsulting/')
        ENABLED = TRUE;
    ```
4. In the Workspace selector at the top of the page, select Create Workspace **From Git repository** and enter the following:
    - Repository URL: `https://github.com/evolvconsulting/e2e-snowflake-ai-hol.git`
    - Workspace name: `e2e-snowflake-ai-hol`
    - API integration: `EVOLV_GITHUB_API_INTEGRATION`
    - Select `Public repository`

    ![](https://raw.githubusercontent.com/evolvconsulting/e2e-snowflake-ai-hol/refs/heads/main/img/01.png)

5. Select **Create**.

#### Create Dedicated Role, Warehouse & Database
- **Role**: `EVOLV_AI_HOL`
- **Warehouse**: `EVOLV_AI_HOL_WH` (XSMALL with auto-suspend/resume)
- **Database**: `EVOLV_E2E_SNOWFLAKE_AI_HOL`
```sql
SET hol_admin_role_name = 'EVOLV_AI_HOL_ADMIN';
SET hol_warehouse_name = 'EVOLV_AI_HOL_WH';
SET current_user_name = CURRENT_USER();

-- Switch to SECURITYADMIN role to create role
USE ROLE SECURITYADMIN;
CREATE OR REPLACE ROLE IDENTIFIER($hol_admin_role_name);

-- Grant the role to the current user
GRANT ROLE IDENTIFIER($hol_admin_role_name) TO USER IDENTIFIER($current_user_name);

-- Grant create database permission to the role
USE ROLE ACCOUNTADMIN;
GRANT CREATE DATABASE ON ACCOUNT TO ROLE IDENTIFIER($hol_admin_role_name);

-- Create a dedicated warehouse for the demo with auto-suspend/resume
CREATE OR REPLACE WAREHOUSE IDENTIFIER($hol_warehouse_name)
    WITH WAREHOUSE_SIZE = 'XSMALL'
    AUTO_SUSPEND = 300
    AUTO_RESUME = TRUE;

-- Grant usage on warehouse to admin role
GRANT USAGE ON WAREHOUSE IDENTIFIER($hol_warehouse_name) TO ROLE IDENTIFIER($hol_admin_role_name);

-- Alter current user's default role and warehouse to the ones used here
ALTER USER IDENTIFIER($current_user_name) SET DEFAULT_ROLE = $hol_admin_role_name;
ALTER USER IDENTIFIER($current_user_name) SET DEFAULT_WAREHOUSE = $hol_warehouse_name;

-- Switch to hol role to create objects
USE ROLE IDENTIFIER($hol_admin_role_name);

-- Create database
CREATE OR REPLACE DATABASE IDENTIFIER($hol_database_name);
```

#### Import Lab Notebook
The remainder of the lab is guided through a Notebook. Follow these steps to import the lab Notebook into the database we just created:
1. In the **e2e-snowflake-ai-hol** Workspace, select the elipsis next to the `evolv-e2e-snowflake-ai-hol.ipynb` file and select **Download**.

![](https://raw.githubusercontent.com/evolvconsulting/e2e-snowflake-ai-hol/refs/heads/main/img/02.png)

2. In the left navigation, select **Projects » Notebooks**.
3. Select the dropdown menu next to **+ Notebook** and select **Import .ipynb file**.
4. Select the notebook file downloaded in step 1.
5. In the Create notebook dialog, enter the following:
    - Name: `evolv-e2e-snowflake-ai-hol`
    - Notebook location: database `EVOLV_E2E_SNOWFLAKE_AI_HOL` schema `PUBLIC`
    - Runtime: `Run on warehouse`
    - Runtime version: `Snowflake Warehouse Runtime 2.0`
    - Query warehouse: `EVOLV_AI_HOL_WH`
    - Notebook warehouse: `EVOLV_AI_HOL_WH`

    ![](https://raw.githubusercontent.com/evolvconsulting/e2e-snowflake-ai-hol/refs/heads/main/img/03.png)
    
6. Select **Create**.
7. Follow the steps outline in the notebook to proceed with the remainder of the lab.