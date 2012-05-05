# Backbone dualStorage Adapter v0.1
This plugin is extension of original plugin **Backbone dualStorage** (https://github.com/lucian1900/Backbone.dualStorage).
Extension includes:
- differential fetch from server (Allow to fetch only changes happened to actual model), meaning it updates local cache of data based on server changes.


**This Plugin should be used for making application work offline, and sync all data with server (client changes and server changes as well), whenever gets connection and try to synchronize.** 

A dualStorage adapter for Backbone. It's a drop-in replacement for Backbone.Sync() to handle saving to a localStorage database as a cache for the remote models.
It is extended for fetching only changes (not full fetch) from server. 

## Usage

Include Backbone.dualStorage after having included Backbone.js:

    <script type="text/javascript" src="backbone.js"></script>
    <script type="text/javascript" src="backbone.dualstorage.js"></script>

Create your models the usual way. 
Use DualStorageCollection for making your Collection backed by localStorage (*This is a variation from original implementation*)
	window.TodoList = Backbone.DualStorageCollection.extend({
	    model: Todo,
	    localStorage: new Store("todos"),
	  	url : "http://localhost:3000/todos",
		lastUpdatedTimestampAttr:"lastUpdated", // Should be time in standard date.toJSON() format, Identifies model last updated time.
		deletedIdAttr:"deleted", //should be boolean, Identifies entries deleted by server. Server should send 'deleted:true' attribute in deleted model. 
	    ...
	    ...
	    ...

For fetching only local changes use remote:false  
	Todos.fetch({remote:false});

You can also deactivate dualsync to some requests, when you want to sync with the server only later.
    Todos.create({name: "someone"}, {remote: false});

## Data synchronization
When the client goes offline, dualStorage allows you to keep changing and destroying records. All changes will be send when the client goes online again.

    // server online. Go!
    Todos.load();       // if first-time loading, load all Todos models from server and save them into localstorage, 
						//after first-time it will only load changes and updates localstorage.
	
	// server offline!
	Todos.create({name: "Turing"});   // you still can create new Todo...
	Todos.models[0].save({order: 21});  // update existing ones...
	Todos.models[1].destroy();        // and destroy as well
	
	// server online again!
	Todos.syncDirtyAndDestroyed();    // all changes are sent to the server and localStorage is updated

Keep in mind that if you try to fetch() a collection that has dirty data, only data currently in the localStorage will be loaded. collection.syncDirtyAndDestroyed() needs to be executed before trying to download new data from the server.

Use Todos.load(); for fetching server changes and update local cache with it.
You can still use Todos.fetch() for reloading all data from server.


## Credits

Thanks to [Mark Woodall](https://github.com/llad) for the QUnit tests.
Thanks to [Jerome Gravel-Niquet](https://github.com/jeromegn) for Backbone.dualStorage