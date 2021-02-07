# TTG Associate Engineer Challenge

Thanks for the opportunity to have a go at this challenge, it was a fun thought process! I decided to opt for a small description of what needs to be done at each step, and then a small code implementation. I hope that's clear enough!

Look forward to hearing from you.

# Method
## Setup
### desc
When someone enters the lottery, we need to store the information in a data store until it is time to choose a winner. 

### code
```js
fetch(`${SERVER_URL}/lottery`, {
  method: 'POST',
  body: JSON.stringify({ userId }),
});
```

## Database
### desc
If we had a relational database, the model for this would be a two column table, where we have a primary key which is the entry id, which has a many-to-one relation with users (as an entry can only belong to one user, but a user could have multiple entries). In a document driven database, we would need a collection of entries, where each document has an entry id and a user object with any information we needed (e.g there name and email to contact them if they win.).

### example sql table
| Prop | Description |
| ---- | ----------- |
| 1 | 83|
| 2 | 12|
| 3 | 119|



## Winner
### desc
Once we were ready to pick a winner, we need to select a winner at random. If we don't already know the amount of entries (if we're closing based on time rather than max entries), then we check the length of the table/collection in the database, and pick a random number between one and the max. We then retrieve that entry from the database, and have the user information at hand to contact the winner. We use a loop to do this for as many tickets as we have available, first checking if the winning number has already come up before decrementing the number of tickets left available. Below is a simple implementation of this, where we store each winning entry in an object, with the winningNumber as the key, so we can have a fast access to see if it's already been picked.

Once we're ready to pick a winner, we need to pick as many different random numbers as we have tickets available. To do this we can follow this process in a while loop, which terminates when there are no more tickets:
  - pick a random number
  - check it hasn't been picked already
  - if it has, start loop again
  - if it hasn't, store the winner in an object, using the winning number as the key (so we have fast access for checking).
  - decrement number of tickets left.

### code
```js
const winners = {};
while (ticketsAvailable > 0) {
// use ceil because we don't want 0
  const winner = Math.ceil(Math.random() * numberOfEntries)
  if (!winners.hasOwnProperty(winner)) {
    winners[winner] = await Entry.findByPk(winnerId);
    ticketsAvailable -= 1;
  }
}
```