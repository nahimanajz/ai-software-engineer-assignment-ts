# What was the bug?

The bug was in the `request` method of `HttpClient`. When an API request was made with a plain object token (e.g., `{ accessToken: "stale", expiresAt: 0 }`), the code failed to refresh the token, leading to no Authorization header being set in the response.

# Why did it happen?

The refresh condition only checked for `null` tokens or expired `OAuth2Token` instances, but ignored plain objects (which are truthy and not `OAuth2Token` instances). This caused plain objects to be treated as valid tokens without refreshing.

# Why does your fix actually solve it?

The fix adds a check for plain objects (`this.oauth2Token && !(this.oauth2Token instanceof OAuth2Token)`) to the refresh condition. This ensures plain objects trigger a refresh, replacing them with a new `OAuth2Token`, which then sets the Authorization header correctly.

# What's one realistic edge case not covered?

A plain object token with an invalid `expiresAt` (non-numeric) could cause runtime errors when the refresh logic executes. Production code should validate token structure before use.
