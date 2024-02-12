# Queue stats and metadata serverless solution

Written By: Jared Hunter

This function was written to reliably list all TaskRouter queues for a Flex workspace and maintain them in a Sync map (adding and removing from the Sync map as queues are added or deleted).

This Sync map can then be used to manage metadata for each queue. Each time this function is run, stats for the queue are fetched and merged with the metadata.

All this data is returned to the calling location, which is intended to be a backend system that can periodically call this function, cache the result, and expose the cache via its own API. This is useful when a solution wants to expose queue stats or queue configuration to something outside of Flex, such as the IVR. An example use case might be for returning operational hours (metadata) and approximate wait times to be used in the IVR in a scalable manner.

The Sync map allows for managing the metadata through an interface, such as an admin panel in a custom component in Flex UI.

## Disclaimer

**This software is to be considered "sample code", a Type B Deliverable, and is delivered "as-is" to the user. Twilio bears no responsibility to support the use or implementation of this software.**

## Pre-requisites

Make sure you have [Node.js](https://nodejs.org) 18 as well as [`npm`](https://npmjs.com) installed.

Next, please install the [Twilio CLI](https://www.twilio.com/docs/twilio-cli/quickstart). If you are using Homebrew on macOS, you can do so by running:

```bash
brew tap twilio/brew && brew install twilio
```

Finally, install the [serverless plugin](https://www.twilio.com/docs/labs/serverless-toolkit/getting-started) for the Twilio CLI:

```bash
twilio plugins:install @twilio-labs/plugin-serverless
```

## Installation

First, clone the repository, change to its directory, and install:

```bash
git clone https://github.com/twilio-professional-services/queue-stats-and-metadata.git

cd queue-stats-and-metadata
npm install
```

Then, copy `.env.example` to `.env` and populate the following variables:

- TWILIO_FLEX_WORKSPACE_SID    - assign the value of your Flex workspace
- TWILIO_FLEX_SYNC_SID         - assign the value of the Sync service to map stats

- TWILIO_SERVICE_RETRY_LIMIT   - max number of retry attempts, applicable to Sync and TaskRouter queries
- TWILIO_SERVICE_MAX_BACKOFF   - max back of period the queries to TaskRouter will take when retrying, recommend 0 if Twilio hosted
- TWILIO_SERVICE_MIN_BACKOFF   - min back of period the queries to TaskRouter will take when retrying, recommend 0 if Twilio hosted

- TWILIO_SYNC_MAX_BACKOFF      - max back of period the queries to Sync will take when retrying, recommend 2000
- TWILIO_SYNC_MIN_BACKOFF      - min back of period the queries to TaskRouter will take when retrying, recommend 1000

- QUEUE_STATS_MAP_NAME         - name of Sync map to store stats
- QUEUE_STATS_CUMULATIVE_PERIOD_MINUTES  - period in minutes from which to count stats backwards from now
- QUEUE_STATS_SLA_SPLIT_PERIODS - split periods to cut cumulative stats by 30,60,120
- QUEUE_STATS_TASKROUTER_STATS_BATCH_SIZE - throttle on max concurrent requests to TaskRouter
- QUEUE_STATS_SYNC_MAP_UPDATE_BATCH_SIZE - throttle on max concurrent requests to Sync

Finally, deploy the serverless functions:

```bash
twilio serverless:deploy
```