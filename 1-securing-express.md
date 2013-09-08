# tl;dr 

1. Don't run as root. 
2. For sessions, set `httpOnly` and `secure` to `true` when setting cookies. 
3. Use the Helmet for secure headers: https://github.com/evilpacket/helmet 
4. Enable `csrf` for preventing Cross-Site Request Forgery: http://expressjs.com/api.html#csrf 
5. Don't use the deprecated `bodyParser()` and only use multipart explicitly. To avoid the 'temp file' bloat problem, use the `defer` property and `pipe()` the multipart upload stream to the intended destination.