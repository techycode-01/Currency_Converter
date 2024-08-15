
# Currency Converter React App

## Overview

This is a simple currency converter built with React + Vite And Tailwind CSS. It allows users to convert between different currencies using live exchange rates. The app features a user-friendly interface and supports dynamic currency conversion with real-time data.

## Features

- **Live Currency Conversion**: Fetches real-time exchange rates.
- **Swap Functionality**: Easily swap the "From" and "To" currencies.
- **Responsive Design**: Works well on various screen sizes.

## Live Demo

Check out the live demo [here](https://currencytype-converter.netlify.app/).

## Installation

To set up this app locally, follow these steps:

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/your-username/react-currency-converter.git


2. **Navigate to the Project Directory**:

   ```bash
   cd react-currency-converter


3. **Install Dependencies**:

   ```bash
   npm install


4. **Start the Development Server**:

   ```bash
   npm run dev
   

5. **Open Your Browser**:

   Navigate to `http://localhost:5173` to view the app.

## Usage

1. **Select Currency**: Choose the currency you want to convert from in the "From" field.
2. **Enter Amount**: Input the amount to be converted.
3. **Select Target Currency**: Choose the currency you want to convert to in the "To" field.
4. **Convert**: Click the "Convert" button to see the result.
5. **Swap Currencies**: Use the "Swap" button to interchange the "From" and "To" currencies.

## Technologies Used

- **React**: JavaScript library for building user interfaces.
- **Tailwind CSS**: Utility-first CSS framework for styling.
- **Vite**: Build tool that provides fast development.
- **Currency API**: API for fetching live currency exchange rates.

## Code Overview

### `useCurrencyInfo.js`

A custom React hook that fetches currency data from the Currency API.

```javascript
import { useEffect, useState } from "react";

function useCurrencyInfo(currency) {
  const [data, setData] = useState({});
  useEffect(() => {
    fetch(`https://cdn.jsdelivr.net/npm/@fawazahmed0/currency-api@latest/v1/currencies/${currency}.json`)
      .then((res) => res.json())
      .then((res) => setData(res[currency]));
  }, [currency]);
  return data;
}

export default useCurrencyInfo;
```

### `InputBox.js`

A reusable input component for selecting currencies and entering amounts.

```javascript
import React, { useId } from 'react';

function InputBox({
  label,
  amount,
  onAmountChange,
  onCurrencyChange,
  currencyOptions = [],
  selectCurrency = "usd",
  amountDisable = false,
  currencyDisable = false,
  className = "",
}) {
  const amountInputId = useId();
  return (
    <div className={`bg-white p-3 rounded-lg text-sm flex ${className}`}>
      <div className="w-1/2">
        <label htmlFor={amountInputId} className="text-black/40 mb-2 inline-block">
          {label}
        </label>
        <input
          id={amountInputId}
          className="outline-none w-full bg-transparent py-1.5"
          type="number"
          placeholder="Amount"
          disabled={amountDisable}
          value={amount}
          onChange={(e) => onAmountChange && onAmountChange(e.target.value)}
        />
      </div>
      <div className="w-1/2 flex flex-wrap justify-end text-right">
        <p className="text-black/40 mb-2 w-full">Currency Type</p>
        <select
          className="rounded-lg px-1 py-1 bg-gray-100 cursor-pointer outline-none"
          value={selectCurrency}
          onChange={(e) => onCurrencyChange && onCurrencyChange(e.target.value)}
          disabled={currencyDisable}
        >
          {currencyOptions.map((currency) => (
            <option key={currency} value={currency}>
              {currency}
            </option>
          ))}
        </select>
      </div>
    </div>
  );
}

export default InputBox;
```

### `App.js`

The main component that uses the `useCurrencyInfo` hook and `InputBox` component to build the currency converter.

```javascript
import { useState } from 'react';
import InputBox from './components/InputBox';
import useCurrencyInfo from './hooks/useCurrencyInfo';

function App() {
  const [amount, setAmount] = useState(0);
  const [from, setFrom] = useState('usd');
  const [to, setTo] = useState('inr');
  const [convertedAmount, setConvertedAmount] = useState(0);

  const currencyInfo = useCurrencyInfo(from);

  const options = Object.keys(currencyInfo);

  const swap = () => {
    setFrom(to);
    setTo(from);
    setConvertedAmount(amount);
    setAmount(convertedAmount);
  };

  const convert = () => {
    setConvertedAmount(amount * currencyInfo[to]);
  };

  return (
    <div
      className="w-full h-screen flex flex-wrap justify-center items-center bg-cover bg-no-repeat"
      style={{
        backgroundImage: `url('https://images.pexels.com/photos/3532540/pexels-photo-3532540.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=2')`,
      }}
    >
      <div className="w-full max-w-4xl mx-auto flex items-center">
        <div className="flex-shrink-0 mr-5">
          <img
            src="https://images.pexels.com/photos/164744/pexels-photo-164744.jpeg"
            alt="Currency exchange"
            className="rounded-lg object-cover"
            style={{ maxHeight: '400px' }}
          />
        </div>
        <div className="flex-grow">
          <div className="w-full max-w-md border border-gray-60 rounded-lg p-5 backdrop-blur-sm bg-white/30">
            <form
              onSubmit={(e) => {
                e.preventDefault();
                convert();
              }}
            >
              <div className="w-full mb-1">
                <InputBox
                  label="From"
                  amount={amount}
                  currencyOptions={options}
                  onCurrencyChange={(currency) => setFrom(currency)}
                  selectCurrency={from}
                  onAmountChange={(amount) => setAmount(amount)}
                />
              </div>
              <div className="relative w-full h-0.5">
                <button
                  type="button"
                  className="absolute left-1/2 -translate-x-1/2 -translate-y-1/2 border-2 border-white rounded-md bg-blue-600 text-white px-2 py-0.5"
                  onClick={swap}
                >
                  Swap
                </button>
              </div>
              <div className="w-full mt-1 mb-4">
                <InputBox
                  label="To"
                  amount={convertedAmount}
                  currencyOptions={options}
                  onCurrencyChange={(currency) => setTo(currency)}
                  selectCurrency={to}
                  amountDisable
                />
              </div>
              <button
                type="submit"
                className="w-full bg-blue-600 text-white px-4 py-3 rounded-lg"
              >
                Convert {from.toUpperCase()} to {to.toUpperCase()}
              </button>
            </form>
          </div>
        </div>
      </div>
    </div>
  );
}

export default App;
```
