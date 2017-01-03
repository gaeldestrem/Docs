# Expense Tracker

We will now turn our application into a simple expense tracker. This will allow us to practice using multiple object instances and relations.

<aside class="read-more">
    <a href="http://starcounter.io/guides/web/partials/">Read more about partials</a>
</aside>

With Starcounter you can divide a view-model into several smaller view-models that are called partials. That's exactly what we will do with our application. We will let PersonJson be one view-model and ExpenseJson another. Doing this makes our application more modular and easier to maintain. Let's start by creating the appropriate files.

1. Add a new Starcounter HTML template with dom-bind in the HelloWorld folder together with `PersonJson.html`. Name it `ExpenseJson.html`.
2. Add a new Starcounter Typed JSON with Code-behind file to the HelloWorld project together with `PersonJson.json` and `PersonJson.json.cs`. Name it `ExpenseJson.json`.

In `ExpenseJson.json`, add the properties `Description`, `Amount`, and `Html`. `Description` and `Amount` will need to be editable.

<div class="code-name">ExpenseJson.json</div>
```json
{
  "Html": "/HelloWorld/ExpenseJson.html",
  "Description$": "",
  "Amount$": 0
}
```
Now we have enough information to create the view for the expenses, so let's do that.

To create a list of the expenses we will use the power of Polymer. Our first step is to create a template for the `Description` and `Amount`.

<div class="code-name">ExpenseJson.html</div>
```html
<template>
    <template is="dom-bind">
        <input value="{{model.Description$::input}}">
        <input value="{{model.Amount$::input}}">
    </template>
<template>
```
<aside class="read-more">
    <a href="https://www.polymer-project.org/1.0/docs/devguide/templates">Read more about dom-repeat</a>
</aside>

Great! Now we just need to stack these templates inside the `Person` view. We can do that easily using dom-repeat. While we are on it, we will also modify the headline and add a button to add new expenses.

<div class="code-name">PersonJson.html</div>
```html
<h1>{{model.FullName}}'s expense list</h1>
.
.
.
<hr>
<template is="dom-repeat" items="{{model.Expenses}}">
    <div>
        <starcounter-include partial="{{item}}"></starcounter-include>
    </div>
</template>
<button value="{{model.AddNewExpense$::click}}" onmousedown="++this.value">Add new expense</button>
<hr>
<h2>Current Balance: {{model.CurrentBalance}}</h2>
```
`starcounter-include` is an insertion point for another template. In this case it's representing the template in ExpenseJson so that we can keep our code separate.

As you can see above, we are using AddNewExpense, which we haven't defined yet. Let's go and fix that now.

<div class="code-name">PersonJson.json</div>
```json
"FullName": "",
"Expenses": [{}],
"AddNewExpense$": 0,
"CurrentBalance": 0
```

`Expenses` is a list of the expenses of a Person and `CurrentBalance` will be the sum of of all these expenses. 

`AddNewExpense$` is a trigger property that allows us to add a new `Expenses` template to the view. We can implement its handler now.

<div class="code-name">PersonJson.json.cs</div>
```cs
void  Handle(Input.AddNewExpense action)
{
    var expense = new Expense()
    {
        Spender = (Person) this.Data,
        Amount = 1
    };
    AddExpense(expense);
}
```
`AddExpense` is a method call to a method that we need to create that will add this newly created `Expense` to our `Person`'s `Expenses` array. It should look like this:

<div class="code-name">PersonJson.json.cs</div>
```cs
public void AddExpense(Expense expense)
{
    var expenseJson = Self.GET("/HelloWorld/partial/expense/" + expense.GetObjectID());
    this.Expenses.Add(expenseJson);
}
```

This handler essentially creates a new expense instance, sets the current Person as the Spender, and adds that expense to that person's array of expenses.

Right now, this handler will not function because we haven't defined the `Expense` class yet. Let's go to `Program.cs` and do that.

<div class="code-name">Program.cs</div><div class="code-name code-title">Add more fields</div>
```cs
namespace HelloWorld
{
.
.
    [Database]
    public class Expense
    {
        public Person Spender;
        public string Description;
        public decimal Amount;
    }
.
.
}
```
`public Person Spender` binds every `Expense` to a `Person`. Without it we would have to look in every `Person` to find who has a certain `Expense`.

<aside class="read-more">
    <a href="http://starcounter.io/guides/sql/">Learn more about using SQL in Starcounter</a>
</aside>

While we are tinkering with the databases, we should also add Spendings and CurrentBalance to the Person class. 

<div class="code-name">Program.cs</div>
```cs
public string FirstName;
public string LastName;

public QueryResultRows<Expense> Spendings => Db.SQL<Expense>("SELECT e FROM HelloWorld.Expense e WHERE e.Spender = ?", this);

public decimal CurrentBalance => Db.SQL<decimal>("SELECT SUM(e.Amount) FROM HelloWorld.Expense e WHERE e.Spender = ?", this).First;
```

`Spendings` is all the expenses of one Person.

<code>CurrentBalance</code> is the sum of all those expenses.

These two are calculated every time they are used by searching through the database so that they are always up to date.

Inside the Program class, you should also add the following GET handler which helps with looking up the expenses.

<div class="code-name">Program.cs</div>
```cs
Handle.GET("/HelloWorld/partial/expense/{?}", (string id) =>
{
    var json = new ExpenseJson();
    json.Data = DbHelper.FromID(DbHelper.Base64DecodeObjectID(id));
    return json;
});
```

Before we run the program, do a quick swing into `ExpenseJson.json.cs` to make sure the partial class is named ExpenseJson.

<section class="see-yourself">Run the program and try to add expenses, change their cost, and see the current balance change in real time.</section>

![part 5 gif](/assets/Part5resized.gif)

With every keystroke, the UI is updated almost instantly from the database. Starcounter's in-memory database makes this possible. There's no delay, everything simply happens at the moment the user interacts with the view.

If you get any errors, you can check your code against the [source code](https://github.com/StarcounterSamples/HelloWorld/commit/9c0dc841b5afaa9e4e8672e32977e90f4b4250af).