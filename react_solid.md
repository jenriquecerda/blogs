## Getting SOLID in React

### Single Responsibility Components

As we already know, SRP says that a **module should have one and only one reason to change**. React components encapsulate and manage its own state, communicating up the hierarchy whenever a new render is needed. Clearly, applying SRP is part of React's suggested patterns. Take a look at the following example.

```JSX
class UtilityExpenses extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      waterExpense = null;
      gasExpense = null;
      electricityExpense = null;
    };
  }

  componentDidMount() {
    fetch('https://www.watercompany.com/bill?id=1234')
    .then((response) => response.json())
    .then((waterBill) => this.setState({ waterExpense: waterBill.balance }));

    fetch('https://www.gascompany.com/bill?id=1234')
    .then((response) => response.json())
    .then((gasBill) => this.setState({ gasExpense: gasBill.balance }));

    fetch('https://www.electriccompany.com/bill?id=1234')
    .then((response) => response.json())
    .then((electricBill) => this.setState({ electricityExpense: electricBill.balance }));
  }

  render() {
    if(this.state.waterExpense && this.state.gasExpense && this.state.electricityExpense) {
      <table>
        <tr>
          <th>Water</th>
          <th>Gas</th>
          <th>Electricity</th>
        </tr>
        <tr>
          <td>{this.state.waterExpense}</td>
          <td>{this.state.gasExpense}</td>
          <td>{this.state.electricityExpense}</td>
        </tr>
      </table>
    }
  }
}
```
So, how many reasons to change do you see in this example? Each API call represents one reason to change, I won't delve to what would happen if another utility service is added, that's another subject we'll discuss later on. Moreover, this components mere responsibility could be seen as a UtiliyExpenseDisplayer, its sole responsibility is that of displaying expenses, maybe with expanding to its responsibility some kind of format whenever the expense is high enough.

Passing this as props will allow our component to delegate those fetching responsibilities to a more appropriate component or script, for that task.