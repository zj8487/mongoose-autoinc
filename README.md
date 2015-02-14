#  [![NPM version][npm-image]][npm-url] [![Build Status][travis-image]][travis-url] [![Dependency Status][daviddm-url]][daviddm-image]

> Mongoose plugin that auto-increments any ID field on your schema forked from mongoose-auto-increment

> Note: The module will change the api friquenctly before 0.1.0 version

## Getting Started

> npm install mongoose-autoinc

Once you have the plugin installed it is very simple to use. Just get reference to it, initialize it by passing in your
mongoose connection and pass `autoIncrement.plugin` to the `plugin()` function on your schema.

> Note: You only need to initialize MAI once.

````js
var mongoose = require('mongoose'),
    Schema = mongoose.Schema,
    autoIncrement = require('mongoose-autoinc');

var connection = mongoose.createConnection("mongodb://localhost/myDatabase");

autoIncrement.initialize(connection, mongoose);

var bookSchema = new Schema({
    author: { type: Schema.Types.ObjectId, ref: 'Author' },
    title: String,
    genre: String,
    publishDate: Date
});

bookSchema.plugin(autoIncrement.plugin, 'Book');
var Book = connection.model('Book', bookSchema);
````

That's it. Now you can create book entities at will and they will have an `_id` field added of type `Number` and will automatically increment with each new document. Even declaring references is easy, just remember to change the reference property's type to `Number` instead of `ObjectId` if the referenced model is also using the plugin.

````js
var authorSchema = new mongoose.Schema({
    name: String
});
    
var bookSchema = new Schema({
    author: { type: Number, ref: 'Author' },
    title: String,
    genre: String,
    publishDate: Date
});
    
bookSchema.plugin(autoIncrement.plugin, 'Book');
authorSchema.plugin(autoIncrement.plugin, 'Author');
````

### Want a field other than `_id`?

````js
bookSchema.plugin(autoIncrement.plugin, { model: 'Book', field: 'bookId' });
````

### Want that field to start at a different number than zero or increment by more than one?

````js
bookSchema.plugin(autoIncrement.plugin, {
    model: 'Book',
    field: 'bookId',
    startAt: 100,
    incrementBy: 100
});
````

Your first book document would have a `bookId` equal to `100`. Your second book document would have a `bookId` equal to `200`, and so on.

### Want to reset counter back to the start value?

````js
bookSchema.plugin(autoIncrement.plugin, {
    model: 'Book',
    field: 'bookId',
    startAt: 100
});

var Book = connection.model('Book', bookSchema),
    book = new Book();

book.save(function (err) {

    // book._id === 100 -> true

    book.nextCount(function(err, count) {

        // count === 101 -> true

        book.resetCount(function(err, nextCount) {

            // nextCount === 100 -> true

        });

    });

});
````



## License

MIT © [zj8487](https://github.com/zj8487)


[npm-url]: https://npmjs.org/package/mongoose-autoinc
[npm-image]: https://badge.fury.io/js/mongoose-autoinc.svg
[travis-url]: https://travis-ci.org/zj8487/mongoose-autoinc
[travis-image]: https://travis-ci.org/zj8487/mongoose-autoinc.svg?branch=master
[daviddm-url]: https://david-dm.org/zj8487/mongoose-autoinc.svg?theme=shields.io
[daviddm-image]: https://david-dm.org/zj8487/mongoose-autoinc
