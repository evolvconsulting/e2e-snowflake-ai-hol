# evolv End-to-end Snowflake AI Hands on Lab

## Overview

This HOL (hands-on lab) explores how to build a complete AI-powered solution on Snowflake that automates and optimizes sales performance, marketing campaigns, and revenue attribution while enhancing the customer experience with AI-driven insights.

You will work with Snowflake’s newest AI capabilities - Cortex Search, Analyst, Agents & Snowflake Intelligence - with a demonstration of Openflow for unstructured data ingestion.  By completing this lab, you will learn how to AI-ready your data and unlock insights & value through modern agentic analytics.
Participants will build an end-to-end AI-powered solution that:
- Prepares unstructured data for AI retrieval
- Enriches structured data with semantic views for AI understanding
- Delivers insights through Snowflake Intelligence

![](https://raw.githubusercontent.com/evolvconsulting/e2e-snowflake-ai-hol/refs/heads/main/img/ref-arch.png)

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

#### Create Dedicated Role, Warehouse & Database
- **Role**: `EVOLV_AI_HOL`
- **Warehouse**: `EVOLV_AI_HOL_WH` (XSMALL with auto-suspend/resume)
- **Database**: `EVOLV_E2E_SNOWFLAKE_AI_HOL`
- **Schema**: `EDW`

1. In the left navigation, select **Projects » Workspaces**.
2. Select **+ Add new**, select **SQL File**, and name it to  your liking.
3. Paste in the following statements and select **Run all** or *Ctrl + Shift + Enter*.
    ```sql
    SET current_user_name = CURRENT_USER();
    
    -- Switch to SECURITYADMIN role to create role
    USE ROLE SECURITYADMIN;
    CREATE OR REPLACE ROLE EVOLV_AI_HOL_ADMIN;
    
    -- Grant the role to the current user
    GRANT ROLE EVOLV_AI_HOL_ADMIN TO USER IDENTIFIER($current_user_name);
    
    -- Grant create database permission to the role
    USE ROLE ACCOUNTADMIN;
    GRANT CREATE DATABASE ON ACCOUNT TO ROLE EVOLV_AI_HOL_ADMIN;
    
    -- Create a dedicated warehouse for the demo with auto-suspend/resume
    CREATE OR REPLACE WAREHOUSE EVOLV_AI_HOL_WH
        WITH WAREHOUSE_SIZE = 'XSMALL'
        AUTO_SUSPEND = 300
        AUTO_RESUME = TRUE;
    
    -- Grant usage on warehouse to admin role
    GRANT USAGE ON WAREHOUSE EVOLV_AI_HOL_WH TO ROLE EVOLV_AI_HOL_ADMIN;
    
    -- Alter current user's default role and warehouse to the ones used here
    ALTER USER IDENTIFIER($current_user_name) SET DEFAULT_ROLE = EVOLV_AI_HOL_ADMIN;
    ALTER USER IDENTIFIER($current_user_name) SET DEFAULT_WAREHOUSE = EVOLV_AI_HOL_WH;
    
    -- Switch to hol role to create objects
    USE ROLE EVOLV_AI_HOL_ADMIN;
    
    -- Create database and schema
    CREATE OR REPLACE DATABASE EVOLV_E2E_SNOWFLAKE_AI_HOL;
    CREATE SCHEMA EDW;
    ```

#### Configure Git API Integration and clone this GitHub repo
```sql
USE ROLE ACCOUNTADMIN;

-- Create API Integration for GitHub (public repository access)
CREATE OR REPLACE API INTEGRATION EVOLV_GITHUB_API_INTEGRATION
    API_PROVIDER = git_https_api
    API_ALLOWED_PREFIXES = ('https://github.com/evolvconsulting/')
    ENABLED = TRUE;

-- The EVOLV_GITHUB_API_INTEGRATION was setup earlier.  Grant usage permissions to it to our HOL role.
USE ROLE SECURITYADMIN;
GRANT USAGE ON INTEGRATION EVOLV_GITHUB_API_INTEGRATION TO ROLE EVOLV_AI_HOL_ADMIN;

USE ROLE EVOLV_AI_HOL_ADMIN;
USE DATABASE EVOLV_E2E_SNOWFLAKE_AI_HOL;
USE SCHEMA EDW;

-- Create Git repository integration for the public demo repository
CREATE OR REPLACE GIT REPOSITORY EVOLV_E2E_AI_HOL_REPO
    API_INTEGRATION = EVOLV_GITHUB_API_INTEGRATION
    ORIGIN = 'https://github.com/evolvconsulting/e2e-snowflake-ai-hol.git';

ALTER GIT REPOSITORY EVOLV_E2E_AI_HOL_REPO FETCH;
```

#### Open Notebook
1. Within Snowsight, change your role to the newly created **EVOLV_AI_HOL_ADMIN** role.
2. In the navigation menu, select **Projects » Notebooks**.
3. Next to **+ Notebook**, open the drop-down menu and select **Create from repository**.
4. For the **Name**, enter: `e2e-snowflake-ai-hol`
5. Select **File location in repository** and then select **Select Database**.
6. Select database `EVOLV_E2E_SNOWFLAKE_AI_HOL`, then select the repo we just cloned `EVOLV_E2E_AI_HOL_REPO`.
7. Select the Notebook file `evolv-e2e-snowflake-ai-hol.ipynb` and then select **Select file**.
8. For Notebook location, select a database and schema `EVOLV_E2E_SNOWFLAKE_AI_HOL.PUBLIC` to contain the notebook.
9. For **Runtime**, select `Run on warehouse`.
10. For **Runtime version**, select `Snowflake Warehouse Runtime 2.0`
11. For **Query warehouse**, select `EVOLV_AI_HOL_WH`.
12. For **Notebook warehouse**, select `EVOLV_AI_HOL_WH`.
13. Select **Create**
14. Follow the steps outlined in the notebook to proceed with the remainder of the lab.
