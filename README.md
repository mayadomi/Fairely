# Fairely
Global map application of all the renaissance and medieval faires. 

## Data Sources

- https://en.wikipedia.org/wiki/List_of_Renaissance_and_Medieval_fairs
- https://www.therenlist.com/
- https://fantasyloreandmore.com/free-list-of-sources-for-fantasy-related-conventions-fairs-festivals/
- https://fairefinder.com/events/featured

## Approach

### M365 Copilot Suggestion

#### 🧱 Step 1: Set Up Laravel Project

    composer create-project laravel/laravel fairely
    cd fairely

#### 🗺️ Step 2: Use a Spatial Package

Install Laravel-Geo or grimzy/laravel-mysql-spatial depending on your database:

    composer require grimzy/laravel-mysql-spatial

For PostgreSQL with PostGIS:

    composer require phaza/laravel-postgis

#### 🧭 Step 3: Configure Your Database

Make sure your database supports spatial types:

- MySQL: Use version 5.7+ with spatial extensions.
- PostgreSQL: Install the PostGIS extension.

#### 📦 Step 4: Create a Model with Spatial Fields

Example: Faire model with a point field for location.

    php artisan make:model Faire -m

In the migration:

    use Grimzy\LaravelMysqlSpatial\Schema\Blueprint;
    
    public function up()
    {
        Schema::create('faires', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('type');
            $table->string('accessibility');
            $table->string('rating');
            $table->date('daterange'); 
            $table->string('size');
            $table->double('cost'); // based on single day
            $table->string('link');
            $table->string('location'); // City, Country
            $table->point('coordinates'); // Latitude & Longitude
            $table->timestamps();
        });
    }

In the model:

    use Grimzy\LaravelMysqlSpatial\Eloquent\SpatialTrait;

    class Faire extends Model
    {
        use SpatialTrait;
        protected $spatialFields = ['coordinates'];
    }

#### 🌐 Step 5: Seed Your Database

You can import data from your Excel file or manually seed it with coordinates using Laravel seeders.

--> Probably need a step 5.1, 5.2 here, where 5.1 is a scrape/collation of information

#### 🗺️ Step 6: Display on a Map --- more options than this

Use Leaflet.js or Mapbox in your frontend:

Map Features:
- symbology by type, current/future (past greyed out)
- labels & pop-ups
- symbol clustering
- reference layers:
  - basemap
  - transport
  - accommodation
  - POIs

Example with Leaflet:

        <div id="map" style="height: 500px;"></div>
        <script>
          var map = L.map('map').setView([0, 0], 2);
          L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
          @foreach($faires as $faire)
              L.marker([{{ $faire->coordinates->getLat() }}, {{ $faire->coordinates->getLng() }}])
                  .addTo(map)
                  .bindPopup("<b>{{ $faire->name }}</b><br>{{ $faire->location }}");
          @endforeach
        </script>

#### 🧰 Optional Features

Filters  (map + list)
- Filter by location
- Filter by date
- Filter by type (Renaissance, Medieval, etc.)
- Filter by accessibility
- Filter by size
- Filter by user rating
- Filter by cost

Search
Keyword on name, location

User Rating
- Star based rating of a faire

Quests
- Ability to save faires/create a quest-list of faires
- Stretch: date sort, budget amount, quest map (route between)

Admin
Admin panel to add/edit faires

Additional fields/attributes
- Activities (eg Jousting, Costume Competition, Bonfire, Parades, Markets, Reenactments etc)

#### 🕑 Data maintenance

- Establish a reconcile methodology & cadence
- Manual/crowdsourced update mechanisms?
