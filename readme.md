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
3. .blur() method

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


Construct the calcPrintBalance() function to calculate the user account balance and then print that account balance to the UI using the .reduce() method.
```JavaScript
const calcDisplayBalance = movements => {
  const balance = movements.reduce((acc, mov) => acc + mov, 0);
  labelBalance.textContent = `${balance}€`;
};
```
 

Constructed the calcDisplaySummary() function which calculates and displays the IN, OUT, and INTEREST values. They all three are each calculated the same, excepting interest, which has added steps to meet the extra parameters which are an interest rate of 1.1% on all deposits if the interest is => 1.
The array of movements is filtered to create a new array containing only deposits. That new array is mapped over with each value being multipilied by the interest rate. Those new calculated values are then added to the new array which the .map() method creates. Another filter() method is run to remove any interest rate calculations that are less than 1. Finally reduce() is called to add the sum of each deposits interest payout into a total, which is inserted into the HTML via a template literal to to fixed decimal places.
```JavaScript
const calcDisplaySummary = function (movements) {
  const incomes = movements
    .filter(mov => mov > 0)
    .reduce((acc, mov) => acc + mov, 0);
  labelSumIn.textContent = `${incomes.toFixed(2)}€`;

  const losses = movements
    .filter(mov => mov < 0)
    .reduce((acc, mov) => acc + mov, 0);
  labelSumOut.textContent = `${Math.abs(losses).toFixed(2)}€`;

  const interest = movements
    .filter(mov => mov > 0)
    .map(mov => mov * 0.011)
    .filter(mov => mov >= 1)
    .reduce((acc, mov) => acc + mov, 0);
  labelSumInterest.textContent = `${interest.toFixed(2)}€`;
};
calcDisplaySummary(account1.movements);
```

To implement the login functionality an event listener was added to the btnLogin. To register the current account the .find() method was called which compared the input username to the username which is stored inside of the user account object. If the input username and account username are === then the same process is repeated for the user pin. Optional chaining is used to prevent an error from being thrown if an invalid username attempts to log in - throws undefined instead.

Upon successful login the welcome label is updated with the users first name using .split()[] and the UI is displayed by removing the opacity 0 from the containerApp style.
```JavaScript
btnLogin.addEventListener('click', e => {
  e.preventDefault();

  currentAccount = accounts.find(
    acc => acc.username === inputLoginUsername.value
  );

  if (currentAccount?.pin === Number(inputLoginPin.value)) console.log('login');
  //Display UI and Welcome
  labelWelcome.textContent = `Welcome back ${
    currentAccount.owner.split(' ')[0]
  }`;
  containerApp.style.opacity = 100;
  inputLoginUsername.value = inputLoginPin.value = '';
  //Display balance
  calcDisplayBalance(currentAccount.movements);
  //Display summary
  calcDisplaySummary(currentAccount.movements);
  //Display movements
  displayMovements(currentAccount.movements);
});
```


The calcDisplaySummary() function is amended to deal with the variable interest rates of different customers.
```JavaScript
const calcDisplaySummary = function (acc) {
  const incomes = acc.movements
    .filter(mov => mov > 0)
    .reduce((acc, mov) => acc + mov, 0);
  labelSumIn.textContent = `${incomes.toFixed(2)}€`;

  const losses = acc.movements
    .filter(mov => mov < 0)
    .reduce((acc, mov) => acc + mov, 0);
  labelSumOut.textContent = `${Math.abs(losses).toFixed(2)}€`;

  const interest = acc.movements
    .filter(mov => mov > 0)
    .map(mov => (mov * acc.interestRate) / 100)
    .filter(mov => mov >= 1)
    .reduce((acc, mov) => acc + mov, 0);
  labelSumInterest.textContent = `${interest.toFixed(2)}€`;
};
  displayMovements(currentAccount);
``` 


To enable money transfers another event listener was added, this time to the btnTransfer. The amount is equal to the input value and the receiver account is found by comparing the username of the accounts in the accounts array with the value input into the Transfer To field.
Checks are done to ensure that recipient and amounts to be transferred are valid. 
Then the recipient account needs to have the transfer added and the sender account needs to be detracted from.
Finally the UI has to be updated to account for the changes.
```JavaScript
btnTransfer.addEventListener('click', e => {
  e.preventDefault();
  const amount = Number(inputTransferAmount.value);
  const receiverAcc = accounts.find(
    acc => acc.username === inputTransferTo.value
  );
  console.log(amount, receiverAcc);
  if (
    amount > 0 &&
    receiverAcc &&
    currentAccount.balance >= amount &&
    receiverAcc?.username !== currentAccount.username
  ) {
    // console.log('VALID');
    //Doing the transfer
    currentAccount.movements.push(-amount);
    receiverAcc.movements.push(amount);
    displayMovements(currentAccount.movements);
    calcDisplayBalance(currentAccount);
    calcDisplaySummary(currentAccount);
  }
});
```

To keep with DRY principles, the displayMovements(), calcDisplayBalance(), and calcDisplaySummary() functions are refactored into a new function, updateUI(). This is then updated in the other relevant places, such as above.
```JavaScript
const updateUI = function(acc){
  calcDisplayBalance(acc);
  calcDisplaySummary(acc);
  displayMovements(acc.movements);
}
```


To enable for users to delete their accounts an event listener is added to the btnClose. The button callback function verifies that the user and pin match those in the accounts array. .findIndex() is used on the accounts array to find the account that has the same username as the username of the current account user. .splice() is then called to remove that matched index.
```JavaScript
btnClose.addEventListener('click', e => {
  e.preventDefault();
  inputCloseUsername.value = inputClosePin.value = '';
  if (
    inputCloseUsername.value === currentAccount.username &&
    Number(inputClosePin.value) === currentAccount.pin
  ) {
    const index = accounts.findIndex(
      acc => acc.username === currentAccount.username
    );
    console.log(index);
    accounts.splice(index, 1);
    containerApp.style.opacity = 0;
  }
});
```


To add in the loan button functionality an event listener was added to the btnLoan which registerred the amount input and if the input amount is above 0 and at least one of the movements inside the current account has a deposit which is at least 10% of the total amount requested. If these requirements are satisfied the current movements will have the requested loan amount added to its array, the UI is updated to reflect the changes and input values reset.
```JavaScript
btnLoan.addEventListener('click', e => {
  e.preventDefault();
  const amount = Number(inputLoanAmount.value);
  if (amount > 0 && currentAccount.movements.some(mov => mov >= amount * 0.1)) {
    // Add movement
    currentAccount.movements.push(amount);
    updateUI(currentAccount);
  }
  inputLoanAmount.value = '';
});
```

