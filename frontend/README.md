# React Project Setup and Deployment Guide for Windows

This guide provides step-by-step instructions for setting up a React project locally on a Windows system.

## 1. Setting Up the React Project Locally

### Install Node.js and npm

1. Download the latest version of [Node.js](https://nodejs.org/) for Windows. The installer includes both `node` and `npm`.
2. Run the installer and follow the instructions.

### Verify Installation

After installation, open **Command Prompt** and verify that Node.js and npm are installed correctly by running:

```bash
node -v
npm -v
```

## 2. Create a React Application

Use npx (which is bundled with npm) to create a new React app:

```bash

npx create-react-app react-frontend
```

This will create a new directory called react-frontend and set up a new React project in it.

Navigate to the Project Directory

After the project is created, navigate to the project directory:

```bash
cd react-frontend
```

## 3. Run the React Application Locally

Install Dependencies

To install the necessary dependencies for your project, run the following command:

```bash

npm install
npm install web-vitals
```

Start the Development Server

To start the development server and run the React application locally, use:

```bash

npm start
```

This will start the application on http://localhost:3000, and you can view it in your browser.

## 4. Install Axios for Frontend to Backend Connection

To install Axios for connecting the frontend to the backend, run:

```bash

npm i axios
```

## 5. Build the React Application for Production

To build the React application for production, run:

```bash
npm run build
```

This will create a build/ directory in your project containing optimized, production-ready files.
