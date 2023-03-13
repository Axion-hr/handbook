# Logging guidelines



### Lines dependency&#x20;

> Always try to have log lines independed from each other (!)

Avoid creating this kind of log outputs:&#x20;

```

// -------- STARTING UPDATE ----------
line
line
line 
// -------- STARTING ENDING ----------

```

#### Log levels

* verbose - by default not logged on production
* debug - by default not logged on production
* info - most actions which represent the process should be here
* warn
* error

### Logging by actions

Try to break down actions and steps into 'action' events with action property (e.g. action={})

* Hierarchical naming with underscores
  * Alllows searching: sync\_elli\_\*, sync\_elli\_db\_\*
* don't use 'fonetic' naming, rather 'technical naming'&#x20;
  * user\_create\_or\_update\_started >>> create\_or\_update\_user\_started

```
action=sync_elli_started
action=sync_elli_prices_started 
action=sync_elli_prices_request_started, requestNumber=1
action=sync_elli_prices_request_finished, durationMs=1200 
action=sync_elli_prices_finished, durationMs=1200
action=sync_elli_ocpi_started 
action=sync_elli_ocpi_request_started, requestNumber=1 
action=sync_elli_ocpi_request_finished, itemsFetched=120, durationMs=1200 
action=sync_elli_ocpi_db_insert_started 
action=sync_elli_ocpi_db_insert_finished, itemsInserted=120, itemsUpdated=23, itemsDeleted=23 durationMs=1200 
action=sync_elli_ocpi_finished, durationMs=1200
action=sync_elli_finished, durationMs=1200
```

* action\_started, action\_finished suffixes - signify is action start of an event or end&#x20;
* duration - in seconds&#x20;
* durationMs - in ms
* durationNs

#### Service layer   &#x20;

```
action=user_create_or_update_started
action=user_create_or_update_finished 
action=user_delete
```



### Standard properties

* action
* duration
* durationMs
* itemsInserted
* itemsDeleted
* itemsUpdated
* itemsFetched
* syncId

syncId - "timeStartedinUnixTime" ili datetime.now() - iso "12.12.2023T11-11-11Z"



### What to log?

* Actions inside service layers
* Actions inside controllers do not have to be logged by default - controllers are usually thin layers before service layer
* Background processes - JobRunr&#x20;



