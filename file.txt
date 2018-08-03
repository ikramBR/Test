var request = require('request');
var API = "https://api.nasa.gov/planetary/earth/assets?";
var API_KEY = "api_key=HQ9eJ6epYQ1ytHkXFERjaEkNsbUXi8RYPOD25lRN";
// 1 day in milliseconds
var msDAY = 1000 * 60 * 60 * 24;
//the function latest_aveTimeDelta to  find the most recent date recorded in the data, and the average time between each date
function latest_aveTimeDelta(results, count){
  var dates = results.map((d, index) => (
  new Date(d.date)
));
  var latest = new Date(Math.max(...dates));
  var earliest = new Date(Math.min(...dates));
  // Formula: P = (tn - t0) / (n - 1)
  var aveTimeDelta = (latest - earliest) / (count - 1);
  return { latest, aveTimeDelta };
}
//the Flyby function to find the next predicted time a satellite is going to pass a given coordiate
function flyby(lat,lon, location)  {
  // if invalid coordinate print invalid
  if (lat < -90 || lat > 90 || lon < -180 || lon > 180) {
    return 'Invalid coordinate!';
  }

  // url to fetch from NASA
  var lat = `${lat}`, lon = `${lon}`;
  const url = API + API_KEY +"&lat=" +lat +"&lon="+ lon;
  const request = require('request');
   request(url,function(error, response, body){
    console.log("error:", error); // Print the error if one occurred
    console.log("statusCode:", response && response.statusCode); // Print the response status code if a response was received
    //console.log("body :", body);

 // parse data into count and results variable
   var { count, results } = JSON.parse(body);
      console.log("location : "+ location);

      if (count < 2) {
        console.log('Inslufficient data recorded!\n');
        console.log('------------------------------------------------\n');


      } else {
        const { latest, aveTimeDelta } = latest_aveTimeDelta(results, count);
        const today = new Date(), predicted = new Date(latest);

        // keep adding predicted by ave_period(days) until it passes today
        while ( predicted <= today) {
          predicted.setDate(predicted.getDate() + (aveTimeDelta / msDAY));
        }

        console.log("Next time : "+ predicted);
        console.log('------------------------------------------------\n');


      }

    });
};
flyby(0.0, 0.0, 'GULF OF GUINEA');
flyby(36.098592, -112.097796,"Grand Canyon");
flyby(43.078154, -79.075891,"Niagara Falls");
