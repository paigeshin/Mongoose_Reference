# Mongoose_Reference

### Schema

```jsx
const mongoose = require("mongoose");

const addressSchema = new mongoose.Schema({
  street: String,
  city: String,
});

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
  age: {
    type: Number,
    min: 1,
    max: 100,
    validate: {
      validator: function (v) {
        return v % 2 === 0; // numbe should be even
      },
      message: (props) => `${props.value} is not an even number!`, // optional
    },
  },
  email: {
    type: String,
    minLength: 10,
    required: true,
    lowercase: true,
  },
  createdAt: {
    type: Date,
    immutable: true,
    default: () => Date.now(),
  },
  updateAt: {
    type: Date,
    default: () => Date.now(),
  },
  bestFriend: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
  },
  hobbies: [String],
  //   address: {
  //     street: String,
  //     city: String,
  //   },
  address: addressSchema,
  somethings: [], // any type
});

// Add Method on Schema
// Don't use arrow function
userSchema.methods.sayHi = function () {
  console.log(`Hi, my name is ${this.name}`);
};

// Add Static Methods
userSchema.statics.findByName = function (name) {
  return this.where({ name: new RegExp(name, "i") });
};

// Chainable Query Helpers
userSchema.query.byName = function (name) {
  return this.where({ name: new RegExp(name, "i") });
};

// Custom property not on schema
userSchema.vertual("namedEmail").get(function () {
  return `${this.name} <${this.email}>`;
});

// Middleware
// Every time we save, updateAt will be updated
userSchema.pre("save", function (next) {
  this.updateAt = Date.now();
  next();
});

// Middleware
// Every time we save, console.log
userSchema.post("save", function (doc, next) {
  console.log("saved", doc);
  next();
});

module.exports = mongoose.model("User", userSchema);

```

### Mongoose Methods

```jsx
const mongoose = require("mongoose");
const User = require("./User");

mongoose
  .connect("")
  .then(() => console.log("connected to db"))
  .catch((err) => console.error(err));

// Save
const user = new User({
  name: "Paige",
  age: 29,
});
user.save().then(() => console.log("saved"));

// Update
const user = await User.create({
  name: "Paige",
  age: 29,
});
user.name = "Sally";
await user.save();

// create
const user = await User.create({
  name: "Paige",
  age: 30,
  email: "paigeshin1991@gmail.com",
  hobbies: ["coding", "reading"],
  address: {
    street: "123 Main St",
  },
});
user.save();

// findById
const user = User.findById("64306aec53164955e1907e1b");

// find
const users = User.find({ name: "Paige" });

// deleteOne
const user = User.deleteOne({ name: "Paige" });

// deleteMany
run();
const user = User.deleteMany({ name: "Paige" });

// query
const users = await User.where({ name: "Paige" }).equals(29).findOne();
const users = await User.where({ name: "Paige" }).gt(29).lt(31).find();
const users = await User.where({ name: "Paige" })
  .where("name")
  .equals("kyle")
  .populate("bestFriend") // populate object
  .limit(2);
//   .select("age"); // select only age field
users[0].bestFriend = "5f9f1b9b1b9b9b9b9b9b9b9b";

// methods applied with validations => dont recommend to user other methods
// - findById
// - save

```
