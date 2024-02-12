# Queue stats and metadata serverless solution

Written By: Jared Hunter

This function was written to reliably list all TaskRouter queues for a Flex workspace and maintain them in a Sync map (adding and removing from the Sync map as queues are added or deleted).

This Sync map can then be used to manage metadata for each queue. Each time this function is run, stats for the queue are fetched and merged with the metadata.

All this data is returned to the calling location, which is intended to be a backend system that can periodically call this function, cache the result, and expose the cache via its own API. This is useful when a solution wants to expose queue stats or queue configuration to something outside of Flex, such as the IVR. An example use case might be for returning operational hours (metadata) and approximate wait times to be used in the IVR in a scalable manner.

The Sync map allows for managing the metadata through an interface, such as an admin panel in a custom component in Flex UI.

## Dependencies

Copy `.env.example` to `.env` and populate the following variables:

- TWILIO_FLEX_WORKSPACE_SID    - assign the value of your flex workspace
- TWILIO_FLEX_SYNC_SID         - assign the value of the sync service to map stats

- TWILIO_SERVICE_RETRY_LIMIT   - max number of retry attempts, applicable to sync and task router queries
- TWILIO_SERVICE_MAX_BACKOFF   - max back of period the queries to task router will take when retrying, recommend 0 if twilio hosted
- TWILIO_SERVICE_MIN_BACKOFF   - min back of period the queries to task router will take when retrying, recommend 0 if twilio hosted

- TWILIO_SYNC_MAX_BACKOFF      - max back of period the queries to sync will take when retrying, recommend 2000
- TWILIO_SYNC_MIN_BACKOFF      - min back of period the queries to task router will take when retrying, recommend 1000

- QUEUE_STATS_MAP_NAME         - name of syncMap to store stats
- QUEUE_STATS_CUMULATIVE_PERIOD_MINUTES  - period in minutes from which to count stats backwards from now
- QUEUE_STATS_SLA_SPLIT_PERIODS - split periods to cut cumulative stats by 30,60,120
- QUEUE_STATS_TASKROUTER_STATS_BATCH_SIZE - throttle on max concurrent requests to task router
- QUEUE_STATS_SYNC_MAP_UPDATE_BATCH_SIZE - throttle on max concurrent requests to sync