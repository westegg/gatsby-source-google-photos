# Gatsby-Source-Google-Photos

#### *IMPORTANT*
*Google Photos refreshes the baseUrl after 60 minutes, in other words, your images will only be visible for an hour. I wanted to make this work as I had a bunch of travel pictures on google photos that I didn't want to migrate, but the platform just isn't intended for hosting images. I'm archiving this repo and migrating the gallery component over to Cloudinary.*

A [Gatsby](https://www.gatsbyjs.org/) plugin for sourcing photos from specific albums in your Google Photos Library and creating `GooglePhoto` nodes.

## Requirements for getting started

In order to use this plugin you need to create a project and get a `Client ID` and `Client secret` from the [Google API Console](https://console.developers.google.com/apis/library?project=pragmatic-mote-231017&folder&organizationId). To obtain the necessary credentials follow the "Configure your app" [instructions](https://developers.google.com/photos/library/guides/get-started#configure-app) in the **Get started with REST** section of the Google Photos API docs.

## Install

`npm install --save gatsby-source-google-photos`

## Setup

1. Setup environment variables

   1.1 Create a `.env` file in the root of your project and specify your environment variables (obtained from the Google API Console) as follows

   ```
   GOOGLE_CLIENT_ID=XXXXXXXXXX
   GOOGLE_CLIENT_SECRET=XXXXXXXXXX
   ```

   1.2 Add `require("dotenv").config()` to the top your `gatsby-config.js`

   1.3 Remember to add .env to your `.gitignore` file

Now you should be able to access your environment variables using `process.env.VARIABLE_NAME` in `gatsby-config.js`. In our case that's `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET`.

2. Add your `gatsby-source-google-photos` configuration to `gatsby-config.js`

```javascript
// gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-source-google-photos`,
      options: {
        clientId: process.env.GOOGLE_CLIENT_ID,
        clientSecret: process.env.GOOGLE_CLIENT_SECRET,
        albums: ['album-name-one', 'album-name-two']
        // if you only have one album pass it as an array
      }
    }
  ]
}
```

## How to use

Query all photos

```javascript
const photoQuery = graphql`
  query photoQuery {
    googlePhotos: allGooglePhoto {
      edges {
        node {
          id
          album
          baseUrl
          filename
        }
      }
    }
  }
`
```

Filter by filename

```javascript
allGooglePhoto(filter: { filename: { eq: "" } })
```

Filter by albumTitle

```javascript
allGooglePhoto(filter: { albumTitle: { eq: "" } })
```

All the `mediaItem` properties [outlined in the Google Photos API](https://developers.google.com/photos/library/guides/access-media-items#media-items) are available on the GooglePhoto node, with the addition of `albumTitle`.

## Example usage

I use [rehype-react](https://github.com/rhysd/rehype-react) to render a gallery component ([https://github.com/tinavanschelt/gatsby-google-photos-gallery](https://github.com/tinavanschelt/gatsby-google-photos-gallery)) within my markdown files

Example using gatsby-starter-blog, gatsby-source-google-photos and gatsby-google-photos-gallery: [https://github.com/tinavanschelt/gatsby-google-photos-gallery-example](https://github.com/tinavanschelt/gatsby-google-photos-gallery-example)

## Caveats

I encountered a couple of limitations whilst patching this thing together.

1.  **OAuth**: The only way to authorize a Google Photos API request is via OAuth, that means that the user (you) has to grant access when the initial build is running, which can complicate things when deploying. I build my site locally and then simply drag the folder to Netlify as outlined [here](https://www.gatsbyjs.org/docs/hosting-on-netlify).

2.  **Filters**: Currently, the Google Photos API does not support simultaneously specifing an albumId and a mediaType filter. Issue being tracked [here](https://issuetracker.google.com/issues/116541300). We get the mediaItems in a specific album and the filter out the photos afterwards.

## Dependencies and Kudos

The plugin makes use of

- [axios](https://github.com/axios/axios) for API requests
- [simple-oauth2](https://github.com/lelylan/simple-oauth2) for authentication
- [chalk](https://github.com/chalk/chalk) for syntax highlighting

The [gatsby-source-mautic](https://github.com/sineau/gatsby-source-mautic) plugin served as a very valuable reference for the OAuth flow
