# Dynamic Launch.json Generation During Build Time

This project demonstrates how to dynamically generate a launch.json file during the build process in Contentstack Launch. This approach allows to manage Launch configuration centrally through Contentstack content types, eliminating the need to hardcode redirects, rewrites, and cache configurations directly in your repository.


## 📂 Project Structure

```
.
├── lib/
│   └── contentstack-server.ts   # Centralized Contentstack Stack setup
├── scripts/
│   └── generateLaunchJson.ts    # Script to generate launch.json
├── package.json
├── tsconfig.scripts.json
└── launch.json                  # Generated at build time
```

## ⚙️ Setup

### Step 1: Content Model Setup

Create the required content types in the stack by following the guide of [Create Content Model](https://www.contentstack.com/docs/developers/content-modeling/about-content-modeling) to manage your Launch configuration:

```bash
- Redirects - for URL redirects
- Rewrites - for URL rewrites
- Cache - for cache priming URLs
```

### Step 2: Connect Your Stack

Set up your Contentstack Stack connection following the [Connect stack Documentation](https://www.contentstack.com/docs/developers/sdks/content-delivery-sdk/typescript/get-started-with-typescript-delivery-sdk)

### Step 3: Clone and Install

Clone the repository:

```bash
git clone <your-repository-url>
cd Dynamic_launch
```

Install dependencies:

```bash
npm install
```

### Step 4: Environment variables

Create a `.env.local` file at the root with your stack credentials:

```env
CONTENTSTACK_API_KEY=your_stack_api_key
CONTENTSTACK_DELIVERY_TOKEN=your_delivery_token
CONTENTSTACK_ENVIRONMENT=environment name
```

### Step 5: Package.json changes

Add a prebuild hook to generate launch.json before build.

```json
{
  "scripts": {
    "prebuild": "npm run generate:launch",
    "generate:launch": "ts-node --project tsconfig.scripts.json scripts/generateLaunchJson.ts",
    "build": "next build"
  }
}
```

- `prebuild` → runs automatically before `npm run build`
- `generate:launch` → generates launch.json
- `build` → builds the Next.js app

### Step 6: Deploy to Contentstack Launch

Deploy your project to Launch. The build process automatically generates `launch.json` with your current configuration.

## Generated launch.json Structure

```json
{
  "redirects": [
    {
      "source": "/source",
      "destination": "/destination",
      "statusCode": 308,
      "response": {
        "headers": {
          "x-powered-by": "launch"
        }
      }
    }
  ],
  "rewrites": [
    {
      "source": "/source",
      "destination": "/destination",
      "request": {
        "headers": {
          "x-api-key": "api-key"
        }
      },
      "response": {
        "headers": {
          "x-powered-by": "launch"
        }
      }
    }
  ],
  "cache": {
    "cachePriming": {
      "urls": ["/delay", "/blog.html", "/new-page.html"]
    }
  }
}
```

## Troubleshooting

**Script fails to run:**

- Verify your environment variables are correctly set
- Ensure your Contentstack credentials have the necessary permissions

**Generated launch.json is empty:**

- Verify that you have published entries in your content types
- Check that the environment field matches your `CONTENTSTACK_ENVIRONMENT` variable

**Build process fails:**

- Check the build logs for specific error messages
- Verify that all dependencies are installed

**Reference Documentation**

- [Creating Content Models](https://www.contentstack.com/docs/developers/content-modeling/about-content-modeling) 
- [Stack Setup](https://www.contentstack.com/docs/developers/sdks/content-delivery-sdk/typescript/get-started-with-typescript-delivery-sdk)
- [Edge URL Redirects](https://www.contentstack.com/docs/developers/launch/edge-url-redirects)
- [Edge URL Rewrites](https://www.contentstack.com/docs/developers/launch/edge-url-rewrites)
- [Cache Priming](https://www.contentstack.com/docs/developers/launch/cache-priming)

