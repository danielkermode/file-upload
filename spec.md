# Feature - file upload for client

1. Each debtor has a number.
2. Within the file system, F:\ drive debtor number folders exist at the top level.
3. What is needed - HTTP POST route that takes file data as an input, and would take the debtor number, and you’d want some restrictions on what the file data looks like - file type and file size.

# File Requirements

1. Allowed file types - .doc, .docx, .pdf, .txt, .jpeg, .jpg, .png
2. Allowed file size - ASP.NET Core 2.0 enforces 30MB max request body size limit. I'm not sure of the max allowed for ASP pages, but it seems to be lower. To be on the safe side, I suggest a max file size of 10MB. The config of the server may need to be changed to allow this.

These file requirement checks should be done on the server side for security reasons (as an attacker can easily get around any frontend checks) but can also be done on the frontend for convenience.

# Request

Takes the format of a POST request with content type `multipart/form-data`. The form data has 2 key value pairs -

`key: debtorNumber, value: {id of debtor}`

`key: fileData, value: {base64 encoded string of the file data}`

# Route logic

So that the server can do the required task, a security concession must be made. The temp IIS user must be given permission to access the F:\ drive (or at least, the folder that contains the debtor files). This concession is reasonable as it is unlikely that an attacker would gain access to the IIS process to abuse this permission, and if they did then the system would be heavily compromised anyway.

The route handler will take the file data and the debtor number, and work out which folder the file should be placed on the F:/ drive based on the debtor number.

It will write the base64 file data to a new file in the relevant debtor folder. Once it has done this successfully, it will send a response to the client - "message: file uploaded successfully".

# Potential concerns

Encoding of file - I have suggested base64 as I have successfully used this format before, but it could be more efficient to use a blob or binary data type. Depends on how the server processes the file data, some experimentation will be required.

File requirements - I have suggested what I think is reasonable but these requirements are open to discussion. I suggest a basic implementation with the given requirements for proof of concept before changing these requirements.
