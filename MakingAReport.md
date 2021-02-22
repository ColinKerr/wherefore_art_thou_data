# Making a Report

This section uses the [iModel Reporter](https://github.com/imodeljs/iModelReporter/) to build a report against the `Bay Town Process Plant` sample iModel.

## Setup

1. Install Prerequsites, Node 12, VS Code (or editor of your choice), Git
1. Clone Repo `git clone https://github.com/imodeljs/iModelReporter.git`
1. Open iModelReporter repo in VS Code.
1. Open `queries/example.json` and replace the URL field with the URL from a session if iTwin Design Review, iModel Console or iModel Schema Explorer with the `Bay Town Process Plant` opened.
1. Install and build

    ```bash
    npm install
    npm run build
    ```

1. Run `npm run start ../queries/example.json`

    > NOTE: Your browser will open a window asking you to sign in using the account you created when you setup your iTwin Platform Developer account and give this application the permissions to run.  This window can be closed after you hit accept the final prompt and you will not be asked again until your token expires

1. This will download the iModel, execute the queries listed in the example.json file and save a csv for each one in `out/example/`.

## Creating Reports

Create reports for the following:

- Sum of dry weights of devices by class and by category
- Identify devices that have no dry weight set or dry weight set to zero
- All functional elements and their corresponding physical element
- Any functional elements without a corresponding physical element and vice versa.

Try to build these queries before checking the answers

[Report Queries](queriesForReport.md)