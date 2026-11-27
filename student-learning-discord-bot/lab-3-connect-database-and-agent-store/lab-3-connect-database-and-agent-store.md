# Lab 3: Configure Database Storage and LiveLabs Search

## Introduction

Connect the bot to Autonomous Database. Create a non-`ADMIN` user for the workshop, set up its agent and memory tables, then load the bundled LiveLabs catalog for semantic search. The optional `*_USER` settings can point to separate accounts, but this lab uses one user for all three features.

Estimated Time: 35 minutes.

### Objectives

In this lab, you will:

- Configure one database user for the workshop features.
- Create the agent profile and personal-memory tables, then load and verify six example agent profiles.
- Validate wallet-backed node-oracledb connections without exposing secrets.
- Install the LiveLabs catalog, model, and vector embeddings.
- Verify database status, private storage, and semantic search.

### Prerequisites

- Finish Labs 1 and 2. Extract the current bot package on Compute.
- Autonomous Database is available, and its wallet is under `/home/opc/hackathon-bot/wallet`.
- Turn on Database Actions for the Autonomous Database.
--to-do: hablar si si necesitas hacer el paso 1 para apex, este paso contemplaba el onnx para natural language -> sql usando ia.

## Task 1: Create the Agent Profile Table and Load Examples

1. In OCI Database Actions, connect as the workshop user you created in Task 1. Open **SQL Actions > SQL Worksheet**.

2. Download [setup-all-tables.sql](https://c4u02.objectstorage.us-ashburn-1.oci.customer-oci.com/p/9DEArLjsgbKXuJgQtSG95E8hMXRFtxgHR8jiHbqz4HgyVYXVnSo0SC_s-zq5CJA3/n/c4u02/b/hosted-files/o/setup-all-tables.sql). In the worksheet, open the downloaded file and select **Run Script**. 

    ![Create the AGENT_PROFILES table and unique name index](./images/lab-3-02-create-agent-profiles-table.png)

3. Download [seed-workshop-agents.sql](https://c4u02.objectstorage.us-ashburn-1.oci.customer-oci.com/p/9DEArLjsgbKXuJgQtSG95E8hMXRFtxgHR8jiHbqz4HgyVYXVnSo0SC_s-zq5CJA3/n/c4u02/b/hosted-files/o/seed-workshop-agents.sql). Open the downloaded file in the same SQL Worksheet and select **Run Script**. 
    ![Create the AGENT_PROFILES table and unique name index](./images/lab-3-02.5-create-agent-profiles-table.png)


## Task 3: Configure and Validate Database Access

1. On Compute, identify a low service name in the wallet.

    ```sh
    <copy>
    grep -E '^[A-Za-z0-9_-]+_low[[:space:]]*=' \
      /home/opc/hackathon-bot/wallet/tnsnames.ora
    </copy>
    ```

2. Edit `/home/opc/hackathon-bot/.env`. Enter the database and wallet credentials.

--to-do: explicar de donde es cada valor 

    ```text
    <copy>
    ADB_USER=<BOT_DATABASE_USER>
    ADB_PASSWORD=<BOT_DATABASE_USER_PASSWORD>
    ADB_CONNECT_STRING=<TNS_ALIAS_FROM_WALLET>
    ADB_WALLET_DIR=/home/opc/hackathon-bot/wallet
    ADB_WALLET_PASSWORD=<WALLET_PASSWORD_FROM_LAB_1>
    </copy>
    ```

3. Protect `.env`, then run deployment preflight. It checks required variables, permissions, wallet files, ADB, agent storage, and the LiveLabs database connection without printing secret values.

    ```sh
    <copy>
    cd /home/opc/hackathon-bot
    chmod 600 .env
    npm run setup:check
    </copy>
    ```

## Task 4: Verify the Database-Backed Features

1. Start the bot temporarily and leave it running for these checks.

--to-do: mostrar que comandos ejecuta este paso
    ```sh
    <copy>
    npm start
    </copy>
    ```

2. In Discord, run `/db` and select **Check the bot connection to ADB**. Confirm the connection is available. Private agent, memory, and LiveLabs tables are intentionally excluded from generic `/db` discovery and queries.

3. The bot also provides `/agent` to create and manage personal agent profiles. The six `WORKSHOP_DEMO` examples used in this lab are loaded directly into the database in Task 2.

4. Keep the command-line LiveLabs result from Task 4 for comparison with the Discord result in Lab 4.

5. State a harmless preference in the channel, ask a generic follow-up, and use `/memory` to inspect the private channel-scoped note. Model-based automatic saving can skip ambiguous statements; use a clear first-person preference.

6. Leave `npm start` running. Lab 4 stops the foreground process before setting up systemd.

## Acknowledgements

- Authors: Cyrce Salinas Rojas and Ilan Gómez Guerrero
- Last Updated: Ilan Gómez Guerrero, November 2026
- Learn More: [Use an in-database transformer model](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/use-retrieval-augmented-generation-and-vectors.html)
