# Embedded MongoDB

Embedded MongoDB will provide a platform neutral way for running mongodb in unittests.

## Why?

easy access??

## Howto

### Maven

Stable (Maven Central Repository, Released: 04.04.2012 - wait 24hrs for maven central)

	<dependency>
		<groupId>de.flapdoodle.embedmongo</groupId>
		<artifactId>de.flapdoodle.embedmongo</artifactId>
		<version>1.10</version>
	</dependency>

Snapshots (Repository http://oss.sonatype.org/content/repositories/snapshots)

	<dependency>
		<groupId>de.flapdoodle.embedmongo</groupId>
		<artifactId>de.flapdoodle.embedmongo</artifactId>
		<version>1.11-SNAPSHOT</version>
	</dependency>

### Changelog

#### 1.11 (SNAPSHOT)

#### 1.10

- race condition and cleanup of mongod process

#### 1.9

- fixed 64Bit detection - amd64
- now with main versions 1.6, 1.8, 2.0, 2.1

### Supported Versions

Versions: some older, 1.8.5, 1.9.0, 2.0.4, 2.1.0
Support for Linux, Windows and MacOSX.

### Usage

	int port = 12345;
	MongodProcess mongod = null;
	MongoDBRuntime runtime = MongoDBRuntime.getDefaultInstance();
	
	try {
		mongod = runtime.start(new MongodConfig(Version.V2_0, port,Network.localhostIsIPv6()));

		Mongo mongo = new Mongo("localhost", port);
		DB db = mongo.getDB("test");
		DBCollection col = db.createCollection("testCol", new BasicDBObject());
		col.save(new BasicDBObject("testDoc", new Date()));

	} finally {
		if (mongod != null)	mongod.stop();
	}

### Usage - custom mongod filename 

	int port = 12345;
	MongodProcess mongod = null;
	RuntimeConfig runtimeConfig=new RuntimeConfig();
	runtimeConfig.setExecutableNaming(new UserTempNaming());
	MongoDBRuntime runtime = MongoDBRuntime.getInstance(runtimeConfig);
	
	try {
		mongod = runtime.start(new MongodConfig(Version.V2_0, port,Network.localhostIsIPv6()));

		Mongo mongo = new Mongo("localhost", port);
		DB db = mongo.getDB("test");
		DBCollection col = db.createCollection("testCol", new BasicDBObject());
		col.save(new BasicDBObject("testDoc", new Date()));

	} finally {
		if (mongod != null)	mongod.stop();
	}

### Unit Tests

	public abstract class AbstractMongoOMTest extends TestCase {
	
		private MongodExecutable _mongodExe;
		private MongodProcess _mongod;
	
		private Mongo _mongo;
		private static final String DATABASENAME = "mongo_test";
	
		@Override
		protected void setUp() throws Exception {
	
			MongoDBRuntime runtime = MongoDBRuntime.getDefaultInstance();
			_mongodExe = runtime.prepare(new MongodConfig(Version.V2_0, 12345));
			_mongod=_mongodExe.start();
			
			super.setUp();
	
			_mongo = new Mongo("localhost", 12345);
		}
	
		@Override
		protected void tearDown() throws Exception {
			super.tearDown();
			
			_mongod.stop();
			_mongodExe.cleanup();
		}
	
		public Mongo getMongo() {
			return _mongo;
		}
	
		public String getDatabaseName() {
			return DATABASENAME;
		}
	}
