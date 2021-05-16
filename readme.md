# Bankist Application

This project was built following the instructions of Jonas Schedtmann, a Udemy JavaScript course instructor. It simulates a banking application and relies heavily on the use of arrays and array methods.

There is a flowchart provided with this project which provides an abstract look at the overall build and functionality. --> Bankist-flowchart.png

There are four accounts that can be logged-in/out with different pins and usernames given here:
account 1 user name = js      account 1 pin: 1111
account 2 user name = jd      account 2 pin: 2222
account 3 user name = sw      account 3 pin: 3333
account 4 user name = ss      account 4 pin: 4444

Constructing this project helped me to better learn and practice the following:
1. insertAdjacentHTML() method and properties (beforebegin, afterbegin, beforeend, afterend).
2. Multiple Array.methods() 

A general walkthrough of the JavaScript code is given below.

First enable the customer movements to be displayed on the current balance sheet (display balance). This is done by constructing the displayMovements() function which uses the forEach() method to loop over each of the movements inside the array. Ternary operator determines the class(type) of movement and the template literals allow for the HTML to be dynamically inserted into the page with the specified data. InsertadjacentHTML then adds the new movements to the current balance sheet.     containerMovements.innerHTML set to an empty string removes the hard coded HTML which formed the initial balances within the balance sheet - thus only the array values are left displayed.
```JavaScript
const displayMovements = function (movements) {
  containerMovements.innerHTML = '';
  movements.forEach((mov, i) => {
    const type = mov > 0 ? 'deposit' : 'withdrawal';

    const html = `
  <div class="movements__row">
    <div class="movements__type movements__type--${type}">${i + 1} ${type}</div>
    <div class="movements__value">${mov}€</div>
  </div>`;
    containerMovements.insertAdjacentHTML('afterbegin', html);
  });
};
displayMovements(account1.movements);
```

Create a method of converting between USD and EUR. This is done using the .map() method which takes the original array values in Euros and then applies the conversion to each iteration of the original array and then stores these values inside of the new array which the .map() method created.
```JavaScript
const eurToUsd = 1.1;
const movementsUSD = account1.movements.map(mov => (mov * eurToUsd).toFixed(2));
```

Set a movementsDescriptions variable which will map over an array and extract the array values index + 1 as well as its value which is then displayed using a combination of template literal and ternary operator.
```JavaScript
const movementsDescriptions = account1.movements.map((mov, i, arr) => {
  return `Movement ${i + 1}: You ${
    mov > 0 ? 'deposited' : 'withdrew'
  } ${Math.abs(mov)}`;
});
```

Compute user names for the account owners by constructing the createUsernames() function. The user name will be the initials of the user. The function parameter is transformed to all lowercase and then .split() by spaces with each spaced word going into a new array. .map() is then called to iterate through the new .split() array items and remove the first letter from each .split() array item. These first letters are then grouped into the new .map() array. .join() then joins these first letters together into a single, unspaced string. 
To compute a username for each of the account holders the forEach() method is used which loops over the accounts array and for each account creates the new property of username. Username is created by taking each accounts owner and running the owner string through the methods described above.
```JavaScript
const createUsernames = accs => {
  accs.forEach(acc => {
    acc.username = acc.owner
      .toLowerCase()
      .split(' ')
      .map(word => word[0])
      .join('');
  });
};
createUsernames(accounts);
```

Declare variables for determining deposits from withdrawals using the .filter() method.
```JavaScript
const deposits = movements.filter(mov => {
  return mov > 0;
});

const withdrawals = movements.filter(mov => {
  return mov < 0;
});
```

Declare a variable to determine the overall account balance using the .reduce() method along with it's accompanying accumulator to add/subtract all of the array values together in order to return the total sum.
```JavaScript
const balance = movements.reduce((acc, mov) => acc + mov, 0);
```
 
