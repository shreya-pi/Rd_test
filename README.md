Of course. Based on our extensive collaboration and the file structure provided, I have a deep understanding of how this project is architected. Here is a detailed and professional `README.md` file that explains the purpose of each component and how they all fit together.

---

# SQL Server to Snowflake Stored Procedure Migration Assistant

An interactive, web-based tool built with Streamlit to guide developers through the complex process of migrating stored procedures from SQL Server to Snowflake. This application provides a step-by-step, stateful workflow, from initial metadata extraction to final unit testing and Git deployment.

## ✨ Key Features

- **Interactive UI:** A user-friendly web interface that abstracts away complex command-line operations.
- **Guided Workflow:** A stateful, multi-step process that guides the user from start to finish, with visual indicators for completed steps.
- **Database Integration:** Connects directly to source (SQL Server) and target (Snowflake) databases to manage metadata and run tests.
- **Live Code Comparison & Editing:** A side-by-side viewer to compare original vs. converted code, with an integrated editor to make and save changes directly.
- **Automated Testing:** A framework to run unit tests against converted procedures in Snowflake and review the results in a filterable dashboard.
- **Git Integration:** A one-click option to publish validated and deployed procedures to a specified Git repository.
- **Persistent Logging:** All actions are logged to a file, which can be viewed and downloaded from the UI for auditing and debugging.
- **Secure Authentication:** User login and registration backed by a database for secure access.

## 🚀 The Migration Workflow

The application is broken down into a series of sequential components, each representing a key phase of the migration process:

1.  **Load Procedures from Source:** The user begins by uploading a `config.py` file containing database credentials. The application then connects to the source SQL Server, extracts metadata for all stored procedures, and loads this information into a central tracking table (`procedures_metadata`) in Snowflake.

2.  **Choose Procedures to Migrate:** This interactive component displays all procedures grouped by schema. The user can search and select which procedures to migrate by setting a `CONVERSION_FLAG`. After flagging, the user can extract the source code of the selected procedures into a local directory (`./extracted_procedures`).

3.  **Convert Procedures:** This step uses Mobilize.Net's **SnowConvert** command-line tool. It takes the extracted SQL files and automatically converts them to Snowflake's SQL dialect, placing the results in the `./converted_procedures` directory. The UI provides a dashboard to view conversion analytics from `assessment.txt`.

4.  **Process Scripts:** After automated conversion, this step allows for final adjustments. The user can perform find-and-replace operations (e.g., changing schema names) and visually compare the original SQL Server script against the converted Snowflake script in a side-by-side viewer. An integrated editor allows for manual corrections to be saved directly.

5.  **Run Unit Tests:** The final and most critical step. This component runs a suite of unit tests (`py_test.py`) against the processed procedures in Snowflake. It verifies both successful deployment (creation) and execution. Results are logged to a `TEST_RESULTS_LOG` table and displayed in a filterable dashboard. Successfully tested procedures can then be published to a dedicated Git repository.

## 📁 File Structure Explained

This project follows a modular structure, separating the main UI, component UIs, and backend logic.

```
./
├── app.py                      # Main Streamlit application file. Handles routing, session state, and authentication.
├── config.py                   # (User-provided) Contains database credentials. NOT committed to Git.
├── Dockerfile                  # Instructions for building a Docker container for deployment.
├── requirements.txt            # A list of all Python dependencies for the project.
├── .env                        # Stores environment variables (DB URLs, Azure keys, etc.). NOT committed to Git.
├── .gitignore                  # Specifies files and directories to be ignored by Git.
├── README.md                   # This file.
│
├── assets/                     # Static files used by the UI.
│   ├── config_template.py      # A template for users to create their config.py file.
│   └── Tulapi_logo.png         # Application logo.
│
├── logs/                       # Directory for log files and tool outputs.
│   ├── Sp_convertion.log       # The main application log file.
│   └── assessment.txt          # The output report from the SnowConvert tool.
│
├── py_tests/                   # Output directory for test reports.
│   └── py_results.html         # HTML report generated from unit test runs.
│
└── scripts/                    # The core logic of the application, organized into modules.
    ├── __init__.py             # Makes 'scripts' a Python package.
    │
    ├── # --- UI Component Modules ---
    ├── update_flag_st.py       # (Step 2) The UI for selecting, flagging, and extracting procedures.
    ├── convert_scripts_st.py   # (Step 3) The UI for running SnowConvert and viewing analytics.
    ├── process_procs_st.py     # (Step 4) The UI for processing, comparing, editing, and saving scripts.
    ├── run_py_tests.py         # (Step 5) The UI for running unit tests, viewing results, and publishing to Git.
    │
    ├── # --- Backend Logic & Utilities ---
    ├── create_metadata_table.py# (Step 1 Backend) Connects to SQL Server and loads metadata to Snowflake.
    ├── extract_procedures.py   # (Step 2 Backend) Extracts flagged procedure source code to files.
    ├── convert_scripts.py      # (Step 3 Backend) A wrapper to run the SnowConvert command-line tool.
    ├── process_sc_script.py    # (Step 4 Backend) Performs find-and-replace on converted files.
    ├── py_test.py              # (Step 5 Backend) The core `unittest.TestCase` class for testing procedures.
    ├── py_output.py            # A utility to fetch test results from the Snowflake log table.
    ├── git_publisher.py        # A utility to handle Git operations (add, commit, push).
    └── log.py                  # Utility for configuring the application's logger.
```




## 📁 File Structure Explained

The project is architected with a clear separation between the main application shell, the modular UI components, and the backend logic scripts.

### Root Directory
-   **`app.py`**: The main entry point for the Streamlit application. Its primary responsibilities include:
    -   Handling user authentication (Login/Register).
    -   Managing global session state (e.g., active component, step completion).
    -   Rendering the main layout, sidebar, and status panels.
    -   Routing which UI component module to display in the main content area.
-   **`config.py`**: A user-provided file containing sensitive database credentials (`SNOWFLAKE_CONFIG`, `SQL_SERVER_CONFIG`) and directory paths. This file is loaded in the first step and is intentionally excluded from version control.
-   **`Dockerfile`**: Instructions for building a Docker container, enabling consistent and isolated deployment of the application.
-   **`requirements.txt`**: A list of all Python dependencies required by the project. This is used by `pip` for installation.
-   **`.env`**: Stores non-sensitive environment variables and configuration details like database URLs for authentication or Azure service endpoints. Excluded from version control.
-   **`.gitignore`**: A standard Git file specifying which files and directories (like `.env`, `__pycache__/`, `venv/`) should not be tracked by version control.
-   **`README.md`**: This documentation file.

### Asset and Log Directories
-   **`assets/`**: Contains static files used by the UI.
    -   `config_template.py`: An example template to guide users in creating their own `config.py` file.
    -   `Tulapi_logo.png`: The application logo displayed in the UI.
-   **`logs/`**: The central directory for all persistent logs and reports generated during the application's runtime.
    -   `Sp_convertion.log`: The main application log file where backend scripts write detailed status updates and errors. Viewable from the UI sidebar.
    -   `assessment.txt`: The summary report automatically generated by the SnowConvert tool during the conversion step.
-   **`py_tests/`**: Contains outputs from the unit testing framework.
    -   `py_results.html`: A detailed HTML report of test runs, generated for offline analysis.

---

### The `scripts/` Directory: The Core Engine

This directory contains the heart of the application's functionality, with a clear separation between UI-facing modules and backend logic.

#### UI Component Modules (`*_st.py` / `*_tests.py`)
These modules are responsible for rendering the Streamlit UI for a specific step in the workflow. They are called by `app.py`.
-   **`update_flag_st.py`**: Renders the interactive UI for **Step 2**. It displays procedures in color-coded, searchable, scrollable expanders and handles the logic for setting conversion flags and extracting source code.
-   **`convert_scripts_st.py`**: Renders the UI for **Step 3**. It provides the button to run the SnowConvert process, shows real-time logs, and displays the conversion analytics dashboard. It also manages the Azure cache and Git publishing actions.
-   **`process_procs_st.py`**: Renders the UI for **Step 4**. This is the most complex UI component, featuring the side-by-side file comparator, the toggle-able code editor, and the button to trigger a unit test for a single procedure.
-   **`run_py_tests.py`**: Renders the UI for **Step 5**. It contains the buttons to execute the bulk test suite and to refresh the results. It also renders the filterable dashboard with metrics and a styled DataFrame of the test outcomes.

#### Backend Logic & Utility Modules
These modules contain the "headless" Python code that performs the actual work. They are called by the UI modules or by `app.py`.
-   **`create_metadata_table.py`**: (**Step 1 Backend**) Contains the `CreateMetadataTable` class. Its methods connect to SQL Server, query the `INFORMATION_SCHEMA`, and use a `MERGE` statement to idempotently insert or update procedure metadata in the Snowflake tracking table.
-   **`extract_procedures.py`**: (**Step 2 Backend**) Connects to Snowflake, queries the metadata table for procedures where `CONVERSION_FLAG` is true, and writes their source definitions to `.sql` files in the `./extracted_procedures` directory.
-   **`convert_scripts.py`**: (**Step 3 Backend**) A robust Python wrapper around the `snowct` command-line tool. It handles checking for its existence, setting up the license, and executing the conversion command with the correct input and output paths.
-   **`process_sc_script.py`**: (**Step 4 Backend**) The `ScScriptProcessor` class performs automated cleanup on the converted files. It contains regex-based logic to remove comments, replace schema names, and apply other necessary transformations.
-   **`py_test.py`**: (**Step 5 Backend**) The core testing engine. It defines a `unittest.TestCase` class (`TestStoredProcedure`) with methods to test procedure creation (`test_create_procedure_from_file`) and execution (`test_procedure_execution`). It also includes the `run_single_test` function, a crucial component that allows for the isolated testing of a single file from the UI.
-   **`py_output.py`**: A simple utility class that connects to Snowflake and executes a `SELECT *` query on the `TEST_RESULTS_LOG` table, returning the data for display in the UI dashboard.
-   **`git_publisher.py`**: A utility class that encapsulates all Git logic. It handles staging files, committing with a dynamic message, and pushing to the remote repository. It is designed to operate directly on the project's root Git repository.
-   **`log.py`**: A standard Python logging setup utility. It configures a logger to write to both the console and the persistent `logs/Sp_convertion.log` file, ensuring all backend actions are recorded.





## 🛠️ Setup and Installation

1.  **Clone the Repository:**
    ```bash
    git clone <your-repository-url>
    cd <your-repository-directory>
    ```

2.  **Create a Virtual Environment (Recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows: venv\Scripts\activate
    ```

3.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Configure Environment Variables:**
    Create a file named `.env` in the root directory and add the necessary variables. This file is listed in `.gitignore` and will not be committed.
    ```env
    # For connecting to the authentication database
    DATABASE_URL="postgresql://user:password@host:port/database"

    # For Azure Blob Storage caching (if used)
    AZURE_STORAGE_CONNECTION_STRING="your_azure_connection_string"
    ACCOUNT_URL="your_azure_account_url"

    # For SnowConvert license (if needed)
    SNOWCONVERT_LICENSE="your_snowconvert_license_key"
    ```

5.  **Prepare your `config.py`:**
    Use the template in `assets/config_template.py` to create your own `config.py` file at the root of the project. You will upload this file via the UI in the first step.

## ▶️ Running the Application

Once the setup is complete, run the Streamlit application from the root directory:

```bash
streamlit run app.py
```

Navigate to the URL provided by Streamlit (usually `http://localhost:8501`) in your web browser to start using the tool.