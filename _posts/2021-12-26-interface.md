---
title: "Demonstrating a Javascript interface"
---


This post just demonstrates how to integrate javascript into a post. 

<input id="loanAmount" value="500000"> Loan Amount</input> <br/>
<input id="loanTerm" value="30"> Loan Term (in years)</input> <br/>
<input id="interestRate" value="3.2"> Interest rate (as percentage)</input> <br/>
<button onclick="output()">Submit</button>
<p>
<div id="monthlyPayment"></div> <br/>
<div id="paymentSchedule"></div> <br/>

<script>

  function calculateMonthlyPayment(amount, term, rate) {
    let monthlyRate = (rate / 100) / 12;
    let termInMonths = term * 12;
    return (amount * monthlyRate * Math.pow(1 + monthlyRate, termInMonths)) / (Math.pow(1 + monthlyRate, termInMonths) - 1)
  }

  function calculatePaymentSchedule(amount, term, rate) {
    let monthlyPayment = calculateMonthlyPayment(amount, term, rate);

    let monthlyRate = (rate / 100) / 12;
    let termInMonths = term * 12;

    let schedule = [];
    let remainingPrinciple = amount;
    for (let i = 1; i <= termInMonths; i++) {
      let interest = remainingPrinciple * monthlyRate;
      let principle = monthlyPayment - interest;
      schedule.push({
        month: i,
        principle: principle,
        interest: interest,
      })
      remainingPrinciple -= principle;
    }
    return schedule;
  }

  function format(x, round = 2) {
    let multiplier = Math.pow(10, round);
    let rounded = String(Math.round(x * multiplier) / multiplier);

    return pad(rounded);
  }

  function pad(x, pad = 10) {
    return " ".repeat(Math.max(pad - String(x).length, 0)) + x;
  }

  function formatRow(data) {
    return `${pad(data.month)} ${pad(format(data.principle))} ${pad(format(data.interest))} <br />`;
  }


  function printPaymentSchedule(amount, term, rate) {
    let header = `${pad("Month")} ${pad("Principle")} ${pad("Interest")} <br />`;
    let schedule = calculatePaymentSchedule(amount, term, rate).map(x => formatRow(x));
    return header + "".concat(...schedule);
  }

  function output() {
    let amount = Number(document.getElementById("loanAmount").value);
    let term = Number(document.getElementById("loanTerm").value);
    let rate = Number(document.getElementById("interestRate").value);
    document.getElementById("monthlyPayment").innerHTML = "Your monthly payment: " + format(calculateMonthlyPayment(amount, term, rate));
    document.getElementById("paymentSchedule").innerHTML = printPaymentSchedule(amount, term, rate);

    console.log(printPaymentSchedule(amount, term, rate));
  }
  
</script>


