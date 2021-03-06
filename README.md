[![Netlify Status](https://api.netlify.com/api/v1/badges/1dbc1521-e0cd-49de-8a34-2cc9dfb36cab/deploy-status)](https://app.netlify.com/sites/zedlove-playback-time-savings/deploys)

# Playback Time Savings Calculator
A project to familiarize myself with Vue.js.

This project is deployed using Netlify [here](https://zedlove-playback-time-savings.netlify.com).

I often watch videos online at an increased rate of playback, depending on the subject matter, using this helpful [browser extension](https://chrome.google.com/webstore/detail/video-speed-controller/nffaoalbilbmmfgbnbgppjihopabppdk). This project takes a duration in hours, minutes and seconds, and a playback rate and returns the amount of time you would save if you watched something of that duration at that rate.

For example, if you watched a 60 minute video at 1.2x playback, you would save 10 minutes!

N.B. It looks ugly, but it works.

## Commands
Install: `yarn install`
Run development: `yarn install`
Build for production: `yarn build`

### Current Functionality
- converts given input and displays amount of time saved
- users can select different playback rates
- looks less ugly than before

### Roadmap
- aesthetic and UX improvements
  - figure out which elements take priority and display them larger
  - add +/- buttons for tap users
  - overflow functionality for when a user inputs mins or secs larger than 59
- a11y overhaul
- unit testing
- add multiple durations with total time saved
- allow users to search some API (TBD) for films/television shows and their runtimes
- maybe deploy live on custom domain with analytics for insights
