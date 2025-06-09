# Cookie Sharing Test Instructions

This setup allows you to test cookie sharing between two oauth2-proxy instances.

## Setup

Both oauth2-proxy instances are configured with:
- **Same cookie secret**: `OQINaROshtE9TcZkNAm-5Zs2Pv3xaWytBmc5W7sPX7w=`
- **Same cookie domain**: `.localtest.me` (allows sharing across subdomains)
- **Same upstream**: HTTPBin service
- **Same identity provider**: Dex

## URLs

- **First proxy**: http://oauth2-proxy.localtest.me:4180
- **Second proxy**: http://oauth2-proxy-2.localtest.me:4181
- **Dex (Identity Provider)**: http://dex.localtest.me:4190
- **HTTPBin (Upstream)**: http://httpbin.localtest.me:8080

## Test Steps

1. **Start the environment**:
   ```bash
   cd contrib/local-environment
   docker-compose up -d
   ```

2. **Test cookie sharing**:
   
   a. Open your browser and navigate to: http://oauth2-proxy.localtest.me:4180
   
   b. You should be redirected to Dex for authentication
   
   c. Login with:
      - Email: `admin@example.com`
      - Password: `password`
   
   d. After successful login, you should see the HTTPBin page
   
   e. **Now test cookie sharing**: Open a new tab and navigate to: http://oauth2-proxy-2.localtest.me:4181
   
   f. **Expected result**: You should NOT be redirected to login again. The second proxy should accept the auth cookie from the first proxy and show you the HTTPBin page directly.

## Verification

If cookie sharing works correctly:
- ✅ First proxy login works normally
- ✅ Second proxy accepts the existing auth cookie without requiring re-authentication
- ✅ Both proxies show the same upstream content (HTTPBin)

If cookie sharing fails:
- ❌ Second proxy redirects you to login again
- This would indicate a configuration issue with cookie domains or secrets

## Troubleshooting

If the test fails, check:
1. Both proxies have identical `cookie_secret` values
2. Both proxies have identical `cookie_domains=[".localtest.me"]` settings
3. You're accessing both URLs from the same browser session
4. The cookies are being set on the `.localtest.me` domain (check browser dev tools)

## Cleanup

```bash
docker-compose down
