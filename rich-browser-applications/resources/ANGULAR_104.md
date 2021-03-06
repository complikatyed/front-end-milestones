# Angular 104

## controllerAs

[Intro to controller as](http://toddmotto.com/digging-into-angulars-controller-as-syntax/)

## ng-include

The `ng-include` directive allows you to include HTML partials into other ones. Most of your did the same thing in your Require/Handlebars applications.

##### **File:** partials/pinterest/card.html
```html
<section class="card--small">
  <div class="card__header" ng-include="'partials/card-header.html'"></div>
  <div class="card__body--highlight" ng-include="'partials/card-content.html'"></div>
</section>
```

##### **File:** partials/pinterest/card-header.html

```html
<h3 class="card__title">{{ card.title }}</h3>
<div class="card__sub-title">{{ card.sub-title }}</div>
```

##### **File:** partials/pinterest/card-content.html
```html
<div class="card__body__container">
  <div class="card__body">{{ card.content }}</div>
</div>
```

## AngularUI Router

[Angular UI router](https://github.com/angular-ui/ui-router) is a more advanced routing library that isn't solely dependent upon the URL, and supports nested views.

## Custom directives

A directive, at its most simple implementation, is simply a fancy `ng-include`.  Check out the custom directive below.

```js
app.directive("simpleDirective", function() {
  return {
    restrict: "E",
    templateUrl: "partials/song-brief.html"
  }
});
```

Now you can use a custom DOM element to load the contents of `song-brief.html` in any other partial.

```html
<div>
  <simple-directive></simple-directive> <!-- Contents of song-brief.html will go here -->
</div>
```

Let's look at a more powerful example of what directives can do for your code. Beyond being a way to include DOM anywhere in your code, which is itself a great feature, your directive can provide complex functionality based on attributes that are defined on the custom element.

So let's start with the `song-brief` partial. This is the base DOM of our directive. It displays the artist and album name for each song, but also will show stars, based on the `rating` key on each song object. Here's a sample JSON representation of a song.

```js
{
  "name": "Song",
  "artist": "Artist",
  "album": {
    "year": 2010,
    "name": "Album"
  },
  "rating": 3
}
```

##### **File:** partials/song-brief.html

```html
<section class="text--small">
  <div>
    By {{ selectedSong.artist }} on {{ selectedSong.album.name }}
    <ul class="rating">
      <li class="{{star.class}}" ng-repeat="star in stars">&#9733;</li>
    </ul>
  </div>
</section>
```

Notice that there is an `ng-repeat` for a `stars` property on the object, but that key does not exist in the raw JSON. The directive adds the `stars` key in its own scope for use in rendering the directive. Let's look at how it does that.

##### **File:** app/directives/song-directive.js
```js
app.directive("musicHistorySongBrief", function() {
  return {
    restrict: "E", // Restrict directive to element
    scope: {
      selectedSong: "=song",  // Bind the `song` attribute in the DOM
                              // to the selectedSong locally scoped variable
      maxRating: "=" // Bind `max-rating` to maxRating local variable
    },
    templateUrl: "partials/song-brief.html",
    link: function(scope, elem, attrs) {

      /*
        Create a new key on the song called `stars`. It's
        an array of objects. Each object contains which
        class to use on each of the stars.
       */
      function setStars() {
        scope.stars = [];
        var rating = parseInt(scope.selectedSong.rating;
        for (var i = 0; i < scope.maxRating; i++) {
          var clazz = (rating <= i) ? "star--empty" : "star--filled";
          scope.stars.push({class: clazz});
        }
      }

      /*
        Since the selectedSong in the `song-view` template
        is bound directly to an object in the controller
        that gets updated after an XHR, I have to watch that
        variable for changes and then run the logic again
        once it gets updated values.
       */
      scope.$watch('selectedSong', function(value){
        console.log("value changed");
        scope.selectedSong = value;
        setStars();
      });

      setStars();
    }
  };
});
```

Now you can include the custom directive in other partials.

##### **File:** partials/song-list.html

```html
<section>
  <div>
    <input type="text" ng-model="songSearchText" />
  </div>

  <div class="c-song" ng-repeat="song in songs | filter: songSearchText">
    <a href="#/songs/{{ song.$id }}">{{ song.name }}</span></a>
    <x-music-history-song-brief song="song" max-rating="5"></x-music-history-song-brief>
  </div>

  <a href="#/songs/new">Create New Song</a>
</section>
```

##### **File:** partials/song-view.html

```html
<a href="#/songs">&lt; Back to song list</a>
<section>
  <h4>Song #{{songId}} - {{ selectedSong.name }}</h4>
  <x-music-history-song-brief song="selectedSong" max-rating="5"></x-music-history-song-brief>
  <span class="text--small">Released in {{selectedSong.album.year}}</span>
</section>
```

# More reading

#### Read these articles first
[Angular for beginners](http://medialoot.com/blog/angularjs-for-absolute-beginners/)
[Angular tutorial](https://www.airpair.com/angularjs/posts/angularjs-tutorial)
[Angular in one day](http://toddmotto.com/ultimate-guide-to-learning-angular-js-in-one-day/)

#### Then do these exercises
[A Better Way to Learn Angular](https://thinkster.io/a-better-way-to-learn-angularjs/)
[Angular on Code Academy](https://www.codecademy.com/courses/javascript-advanced-en-2hJ3J/0/1)

#### Keep this one bookmarked for quick reference
[Angular in 30 Minutes](http://www.revillweb.com/tutorials/angularjs-in-30-minutes-angularjs-tutorial/)

#### Advanced Topics
[Angular directives tutorial](http://www.befundoo.com/university/tutorials/angularjs-directives-tutorial/)
[Create a tree hierarchy with ng-include](http://gurustop.net/blog/2014/07/15/angularjs-using-templates-ng-include-create-infinite-tree/)
[How to Work With All the Scopes in Angular](http://jonathancreamer.com/working-with-all-the-different-kinds-of-scopes-in-angular/)
