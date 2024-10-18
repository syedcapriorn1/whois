/**
 * This function fetches the WHOIS data for a domain and extracts its expiry date.
 * It also returns whether the domain is still active or expired.
 * 
 * @param {string} domain The domain name to check.
 * @return {array} [expiryDate, status] The expiry date and status ('active' or 'expired').
 */
function getDomainExpiry(domain) {
  var whoisUrl = "https://jsonwhoisapi.com/api/v1/whois?identifier=" + domain;  // Replace with a valid WHOIS API URL
  var apiKey = "YOUR_API_KEY";  // Replace with your WHOIS API key

  try {
    var options = {
      "method": "GET",
      "headers": {
        "Authorization": "Bearer " + apiKey
      }
    };
    
    var response = UrlFetchApp.fetch(whoisUrl, options);
    var data = JSON.parse(response.getContentText());

    var expiryDate = data.expires_at;  // Extract expiry date from the API response
    var currentDate = new Date();
    var expiryDateObj = new Date(expiryDate);

    var status = expiryDateObj > currentDate ? "active" : "expired";
    
    return [expiryDate, status];
  } catch (error) {
    return ["Error: Could not retrieve data", "unknown"];
  }
}

/**
 * This function updates the Google Sheet with domain expiry dates and their statuses.
 * Ensure the first column contains the domain names.
 */
function updateDomainStatuses() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = sheet.getRange(2, 1, sheet.getLastRow() - 1, 1).getValues();  // Assumes domains are in column A starting from row 2

  for (var i = 0; i < data.length; i++) {
    var domain = data[i][0];
    if (domain) {
      var result = getDomainExpiry(domain);
      var expiryDate = result[0];
      var status = result[1];

      sheet.getRange(i + 2, 2).setValue(expiryDate);  // Set expiry date in column B
      sheet.getRange(i + 2, 3).setValue(status);      // Set status in column C
    }
  }
}

/**
 * This function sets a daily trigger to run the updateDomainStatuses function.
 */
function createDailyTrigger() {
  ScriptApp.newTrigger("updateDomainStatuses")
    .timeBased()
    .everyDays(1)  // Runs the script daily
    .atHour(1)     // Set the hour you want the script to run (1 AM here)
    .create();
}
