# Spotify API Testing 🎵🎧
This project showcases the testing of Spotify API endpoints using Postman and Newman. The tests cover various endpoints to ensure functionality, reliability, and performance such as reading user profiles, managing playlists, and handling tracks.
## Features
**✅ Automated API Testing:** Covers endpoints like user profiles, playlists, and tracks. <br>
**👥 User Profile Management:** Fetch and update user profile information. <br>
**▶️ Playlist Management:** Create, read, update, and delete playlists. <br>
**🎶 Track Management:** Save, read, and delete tracks for the current user. <br>
**🛠️ Custom Assertions:** Checks for response codes, headers, and body content. <br>
**⏱️ Response Time Validation:** Ensures API performance meets specified benchmarks. Measure response times and data sizes for API requests.
## API Documentation
https://documenter.getpostman.com/view/33497213/2sAYQiB7aX
## Technology Used
  **1. Postman:** For API testing and test script creation. <br>
  **2. Newman:** To run Postman collections via the command line. <br>
  **3. HTML Extra:** For detailed, visually enhanced test reports. <br>
  **4. JavaScript:** Test assertions and validations. <br>
## Pre-requisite
Before using this project, ensure the following:
  1. Spotify Developer Account with an active application for API access.
  2. Node.js installed on your system.
  3. Newman and htmlextra installed globally:
     `npm install -g newman newman-reporter-htmlextra`
## Installation
 1. Clone the repository:
`git clone https://github.com/Callick/spotify-api-testing.git
cd spotify-api-testing` <br>
 2. Install Newman:
`npm install -g newman` <br>
 3. Set up your environment variables for Spotify API credentials.
## Usage
 **Running the API Tests:**
   - Update the Postman environment file with your Spotify API credentials (Client ID, Client Secret, and access token).
   - Execute tests via Newman:
     `newman run Spotify_API_Testing.postman_collection.json -e Spotify_API_Testing.postman_environment.json -r htmlextra --reporter-htmlextra-export ./reports/Spotify-Test-Report.html`
   - Open the generated report:
     `start ./reports/Spotify-Test-Report.html`
## Testing
  **Summary:** <br>
    - Total Requests<sup>13</sup>  
    - Total Assertions<sup>99</sup>  
    - Total Run Duration<sup>15s</sup>
    - Failures<sup>20</sup>  
    - Average Response Time<sup>267ms</sup>
  **Detailed Results:**
    - User Profile<br>
        1. GET /v1/me: <sup>80% pass rate.</sup><br>
        2. GET /v1/me/following?type=artist: <sup>78% pass rate.</sup><br>
    - Playlists<br>
        1. GET /v1/me/playlists: <sup>75% pass rate.</sup><br>
        2. POST /v1/users/{user_id}/playlists: <sup>88% pass rate.</sup><br>
        3. GET /v1/playlists/{playlist_id}: <sup>100% pass rate.</sup><br>
        4. PUT /v1/playlists/{playlist_id}: <sup>75% pass rate.</sup><br>
    - Tracks<br>
        1. GET /v1/me/tracks: <sup>71% pass rate.</sup><br>
        2. PUT /v1/me/tracks: <sup>67% pass rate.</sup><br>
        3. DELETE /v1/me/tracks: <sup>67% pass rate.</sup><br>
1. Read_Current_User's_Profile
   URL: [{{baseURL}}/me] (https://api.spotify.com/v1/me)<br>
   Method: GET<br>
   Pre-request Script:<br>
   `NULL`
   Post-request Script:<br>
   ```pm.test("Checked whether the response code is 200 or not!", function () {pm.response.to.have.status(200)})
switch(pm.response.code){

    case 200:
        // To verify the reponse body
        pm.test("Checked whether response body contains data or not!", function(){
            pm.expect(pm.response.text()).to.not.be.empty
        })
        // To verify header authorization
        pm.test("Checked whether response header contains authorization or not!", function () {
            pm.response.to.have.header("Authorization")
        });
        // To verify response header
        pm.test("Checked whether response header has expected Content-type or not!", function(){
            pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
        })
        // To verify the spotify URL
        pm.test("Checked whether response has valid URL of user-profile  or not!", function () {
        // Parse the response body as JSON
        var jsonData = pm.response.json();
        
        // Define the regex pattern for the expected Spotify URL format
        var spotifyUrlPattern = /^https:\/\/open\.spotify\.com\/user\/.+$/;
        
        // Assert that the spotify URL matches the expected format
        pm.expect(jsonData.external_urls.spotify).to.match(spotifyUrlPattern);
    });
        pm.test("Checked whether the response time is less than 0.2s or not!", function () {
            pm.expect(pm.response.responseTime).to.be.below(200)
        })
        pm.test("Checked whether the response size is less than 3KB or not!", function(){
            pm.expect(pm.response.responseSize).to.be.below(3072)
        })
        pm.environment.set("user_id", pm.response.json().id)
        var user_name = pm.response.json().display_name
        pm.test(`Successful to fetch details of ${user_name}'s Spotify Account.`)
        var resTime = pm.response.responseTime / 1000
        pm.test(`The response Time Was ${resTime} Seconds.`)
        pm.environment.set("username", user_name)

    break
    // Below cases are as per Spotify API Document
    case 401:
        pm.test("The Access Token Has Expired.")
    break
    case 403:
        pm.test("Server is refusing to fulfill your request.")
    break
    case 429:
        pm.test("You've requested too many requests.")
    break
    default:
        pm.test("Unsuccessful to fetch details of Spotify Account.")

}```
Response:
`The code was 200. Request successful. The server has responded as required.`
3. Read_Followed_Artists
4. Read_Current_User's_Playlists
5. Create_Playlist
6. Read_After_Create_Playlist_
7. Read_Specific_Playlist_Items
8. Update_Playlist_Details
9. Read_After_Update
10. Read_User's_Saved_Tracks
11. Save_Tracks_for_Current_User
12. Read_After_Save_Track
13. Delete_User's_Saved_Track
14. Read_After_Delete_Track
## Highlights:
  **1. Performance Tests:** <br>
       - Validated API response times.<br>
       - Tests like Response time is less than 200ms.<br>
  **2. Data Integrity:** <br>
       - Verified user profile details, playlists, and tracks.<br>
  **3. Custom Reports:** <br>
       - Enhanced visual reports using the htmlextra reporter.<br>
## Challenges
**1. Intermittent Failures:** Observed occasional 401 errors due to token expiration.<br>
       - Solution: Implemented automated token refreshing.<br>
**2. Response Time Failures:** Some tests failed the benchmark of 200ms.<br>
**3. Solution:** Increased thresholds and optimized request parameters.<br>
## Future Enhancements
**- Automated CI/CD Integration:** Integrate the tests with a CI/CD pipeline for automated testing.<br>
**- Extended Test Coverage:** Add more test cases to cover additional Spotify API endpoints.<br>
**- Performance Optimization:** Analyze and optimize the performance of API requests.<br>
## Conclusion
This project demonstrates the comprehensive testing of Spotify API endpoints using Postman and Newman. The tests cover various functionalities, ensuring the reliability and performance of the API. The detailed results provide insights into the API's behavior and performance metrics.
# Screenshots
