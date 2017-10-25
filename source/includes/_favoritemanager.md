## Module: FavoriteManager

The FavoriteManager is an aggregate of Session.

This module provides CRUD functions for Favorites. The term "Favorite" refers to the target of a top up operation, in which money is added to a remote account associated with a device or service. The consumer may add one or more favorites to their account.

### Method: favouriteManager.create

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

        let favourite: Favourite = Favourite()

        favourite.provider = "AT&T"
        favourite.type = "phone"
        favourite.value = "555499999999"

        self.mySession?.favouriteManager.create(
            favourite: favourite) { (remoteFavourite: Favourite) in

                let favouriteId: String = remoteFavourite.id
        }
    }
}
```
```java
// create favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

        // create a favourite
        Favourite favourite = new Favourite();
        favourite.setProvider("AT&T");
        favourite.setType("phone");
        favourite.setValue("549912341234");
        myFavouriteManager.create(favourite,this);
    }

    @Override
    public void onFavouriteCreateSuccess(Favourite favourite) {
        // show favourite created message
    }
}
```

Method signature: `session.favouriteManager.create( [callback or interface] )`

You must pass in a `favourite` object with the `id` field blank.

The method returns the created `favourite` object with `id` upon success.

#### favourite Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|new_id_123|
|value|string|549912341234|
|type|string|phone|
|provider|string|AT&T|

### Method: favouriteManager.update

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.favouriteManager.get(id: "123") { (remoteFavourite: Favourite) in

            remoteFavourite.value = "555488888888"

            self.mySession?.favouriteManager.update(favourite: remoteFavourite) {
             (remoteFavourite2: Favourite) in

                //handle result
            }
        }
    }
}
```
```java
// update favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

        // update a favourite
        Favourite favourite = new Favourite();
        favourite.setProvider("AT&T");
        favourite.setType("phone");
        favourite.setValue("549912341234");
        myFavouriteManager.update(favourite,this);
    }

    @Override
    public void onFavouriteUpdateSuccess(Favourite favourite) {
        // show favourite updated message
    }
}
```

Method signature: `session.favouriteManager.update( favourite, [callback or interface] )`

You must pass in a valid `favourite` object with `id` populated.

The method returns the updated `favourite` object upon success.

### Method: favouriteManager.delete

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.favouriteManager.get(id: "123") { (remoteFavourite: Favourite) in

            self.mySession?.favouriteManager.delete(favourite: remoteFavourite) { () in

                //handle callback
            }
        }
    }
}
```
```java
// delete favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

        // delete a favourite
        Favourite favourite = new Favourite();
        favourite.setProvider("AT&T");
        favourite.setType("phone");
        favourite.setValue("549912341234");
        myFavouriteManager.delete(favourite,this);
    }

    @Override
    public void onFavouriteDeleteSuccess(HttpResponse httpResponse) {
        // show favourite deleted message
    }
}
```

Method signature: `session.favouriteManager.delete( id, [callback or interface] )`

You must pass in the `favourite` object to delete.

### Method: favouriteManager.get

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.favouriteManager.get(id: "123") { (remoteFavourite: Favourite) in

            let favouriteId: String = remoteFavourite.id
        }
    }
}
```
```java
// get a favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

        // get a single favourite
        String favourite_id = "123";
        myFavouriteManager.get(favourite_id,this);
    }

    @Override
    public void onFavouriteGetSuccess(Favourite favourite) {
        // get favourite info
        String id = favourite.getId();
        String provider = favourite.getProvider();
        String type= favourite.getType();
        String value = favourite.getValue();
    }
}
```

Method signature: `session.favouriteManager.get( id, [callback or interface] )`

You must pass in the `id` of the `favourite` to get.

The method returns an `favourite` object.

### Method: favouriteManager.getAll

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.favouriteManager.getAll() { (listOfFavourites: Array<Favourite>) in

            //handle the results
        }
    }
}
```
```java
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

        // list all favourites
        myFavouriteManager.getAll(this);
    }

    @Override
    public void onFavouriteGetAllSuccess(List<Favourite> list) {
        // get favourite list info
        for(Favourite favourite : list) {
            String id = favourite.getId();
            String provider = favourite.getProvider();
            String type= favourite.getType();
            String value = favourite.getValue();
        }
    }
}
```

Method signature: `session.favouriteManager.getAll( [callback or interface] )`

The method returns an array of `favourite` objects.






