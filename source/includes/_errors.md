# Errors

On an invalid request, you will get a a response with a status code in the 4XX-5XX range. Here's
what that means.


Code | Meaning
---------- | -------
400 | Bad Request — Your request isn't properly formatted
401 | Unauthorized — Your API key is wrong
403 | Forbidden — You aren't allowed to perform this action
404 | Not Found — The resource you're requesting could not be found
406 | Not Acceptable — You requested a format that isn't json
422 | Unprocessable Entity — There were validation errors. Usually, you will also get an errors array that tells you exactly what the errors were.
500 | Internal Server Error — We had a problem with our server. Try again later.
