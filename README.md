# Unified-Take-Home-Assignment

# Zero To App: React Native Template Case Study 

Zero To App, an open source React Native template that aims to accelerate the process of building minimum viable product mobile apps seamlessly on both mobile and web with a shared codebase in React Native.

 [Web Demo](https://zero-to-app.vercel.app/)
 
 [GitHub Link](https://github.com/Alex-Amayo/zero-to-app)
 
 [Example App built with Zero to App](https://eat-out-food-finder.vercel.app/)
 
While working on this project I spent a significant amount of time exploring similar open source projects that are widely adopted, like Ignite for React Native and Create React App on the web, learning from what goes into building and maintaining large repositories with many contributing developers. 

### Problem/Motivation

During my time freelancing, several potential clients requested mobile app development services, often looking for a minimum viable product for a startup or a companion app for an existing web app. However, I found that the initial project setup is often too time consuming, limiting my ability to take on multiple projects and deliver efficient results even when the product requirements were extremely simple.

### Solution

Zero To App is built to address this issue by streamlining cross-platform project setup and providing commonly used pre-built screens and components that you can easily customize to match different app designs. This would allow users to focus on product development rather than boilerplate coding, ultimately reducing the time it takes to build a minimum viable product that works seamlessly both mobile and web in React Native with a single code base. 


Mobile App:

<img src='https://utfs.io/f/YPschnd1m5QkTqyeQWPZoXAPfacNzEn73ZUuI5bVyStJYQdL' width='250' alt='mobile app'>

Web Browser on Mobile: 

<img src='https://utfs.io/f/YPschnd1m5QkE9bARyXpXc0ZDKzOCAyargbmV8sl43JNt7HB' width='350' alt='web browser'>

Web Browser Full Screen:

<img src='https://utfs.io/f/YPschnd1m5Qkms3P8sSht8nw1N4RxA3PuVJIbdTfrcjYgyiv' width='800' alt='web browser'>

## Streamlining Project Setup

### Implementing Development Tools

To streamline project setup I incorporated commonly used development and testing packages to the template  project. Setting up these packages and making sure they function well together is one of the main reasons project setup takes significant time and effort in React Native. I set up Typescript to ensure type safety, ESLint and Prettier  with common rules to help maintain code quality and consistency. 

### Creating an NPM Package

To make it easier for users to start a new project with the template I created and published an NPM package with a simple command line tool. The script creates a new  project directory with a name specified by the user in the command and then uses the simple-git NPM package to clone the template project files from GitHub to the new directory. It also prints out user user feedback and errors as needed for a better developer experience. A personal motivation in building this project was to gain experience publishing and maintaining an NPM package.

<img src='https://utfs.io/f/YPschnd1m5QkXsVftAhQsYtRB13qKP4huawAcMpdSJZkxfEi' width='600' alt='npm start'>

### Reusable, Easily Customizable Components and Theming

To have all the pre-built components share common styles properties which can be updated to reflect theme changes, I used the Context API to create a ThemeContext. The ThemeContext contains an object with style properties that components can subscribe to and a function that allows you to switch to a different object subsequently changing style properties to reflect different themes. 

The reason I chose this over other global state providers like Zustand or Redux, is because it is built in React and doesn't require any external packages. This allows me to extract the components and ThemeContex logic to make a modular lightweight component library, in a future iteration of the project.

theme.tsx: 

```
import React, { createContext, useState } from 'react';
import { darkTheme, lightTheme, ThemeValuesType } from './themeConfig';

//Defining theme type
export type ThemeType = 'light' | 'dark';

//Defining types fror the ThemeContext
export type ThemeContextType = {
  values: ThemeValuesType;
  toggleTheme: () => void;
};

//Initialize ThemeContext with a toggle function placeholder
const ThemeContext = createContext<ThemeContextType>({
  values: lightTheme,
  toggleTheme: () => {},
});

type ThemeProviderProps = {
  children: React.ReactNode;
};
//Initialize ThemeProvider with a toggle function
const ThemeProvider = ({ children }: ThemeProviderProps) => {
  const [values, setTheme] = useState<ThemeValuesType>(lightTheme);
  const toggleTheme = () => {
    setTheme(values === lightTheme ? darkTheme : lightTheme);
  };

  return <ThemeContext.Provider value={{ values, toggleTheme }}>{children}</ThemeContext.Provider>;
};

export { ThemeContext, ThemeProvider };
```

### Resolving Layout Differences with Hooks and The Platform API

Developing for mobile and web poses a unique challenge dealing with platform differences and screen sizes. Users expect different layouts on a mobile app, a  full sized web page and a web page on a mobile browser. I addressed this using the useWindowDimensions hook and the Platform API to detect and respond to changes appropriately. 

In order to reuse the useWindowDimensions hook to consistently account for differences in screen width, I wrapped it in a custom hook, useWindowWidth, that also exports values of set breakpoints. This reduces code duplication and makes logic consistent and reusable in different screens and components.

useWindowWidth hook:

```
/**
 * Returns the width of the window
 * @param {number} windowWidth - The width of the window.
 * @returns {number} The width of the window.
 */

import { useWindowDimensions } from 'react-native';

export const useWindowWidth = () => {
  const windowWidth = useWindowDimensions().width;
  return windowWidth;
};

/**
 * Breakpoints for responsive design
 * @returns {object} The breakpoints for responsive design
 */

export const breakpoints = {
  small: 480,
  medium: 768,
  large: 1024,
  xlarge: 1280,
};
```

### Challenges with Expo Router and Implementing Top Navigation for Tab Screens 

A particularly challenging issue I faced with this project was setting up navigation. Tab screens are an essential feature on most apps. Mobile app users typically expect bottom navigation on tab screens. On the web, users expect the navigation to be on the top. This is especially important for mobile browsers where the browser frame takes significant space on the bottom of the screen.

Expo Router, which the template project uses for navigation, currently lacks support for tab screens with top navigation. To resolve this I implemented a solution that involves exposing the underlying React Navigation properties, which Expo Router is built on, and implementing a Material Top Tab Navigator rendering a custom app bar component. 

While this solution worked and allowed me to build a custom top tab navigator for the web, it was needlessly complex, requiring extensive research, trial and error with unresolved GitHub issues. In hindsight I would stick to more popular navigation libraries like React Navigation, particularly when working cross-platform between web and mobile where flexible solutions are more likely to be  needed.

### Rethinking Backend Solutions to Better Serve Target Users

From developer  feedback I learnt that some were apprehensive to use the template due to being locked into Supabase as an authentication and backend solution. A large number of users expressed a need to integrate the project with their existing backend infrastructure instead of being locked into supabase. I aim to address this need in a future update that will make the backend and authentication methods open for the user to implement their own solution or optionally add supabase, firebase or other pre-configured client options through the CLI tool. In hindsight I should have recognized the need for different apps to integrate a backend solution that best meets each project's requirements. This reinforces the need to stay focused on solving the specific problems a feature or project aims to solve, rather than over engineering solutions. 

### Refactoring for Maintainability

This project consists of three main parts; the component library, the template, and the CLI tool. 
To improve modularity and maintainability, I plan to extract the component library and theme logic to a separate component library, and place all three parts in separate folders within a monorepo. 

New folder structure: 

```
├── zero-to-app
|  ├── template
|  ├── ui
|  ├── cli
```

This modular approach would enable me to have a  different development and testing environments for each individual package but keep the files in a central location sharing common files and dependencies where necessary. For example, using React Native Storybook to debug and test responsive components for both web and mobile would be isolated to /ui. I’ve been learning and experimenting with setting up Yarn workspaces to support this structure as well as tools like Lerna to help with managing dependencies, versioning and running scripts.

### Conclusion

I’m testing this project by building simple demo apps like [Eat Out Food Finder](https://eat-out-food-finder.vercel.app/). This helps me test how re-usable the components are, find potential bugs and areas of impprovement by develoing and maintain apps built using the Zero To App Template, subsequently iterating and refining the package with the hope doption and community innovlement increase over time. 

