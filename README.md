# :floppy_disk: gatsby-plugin-remote-images
Download images from any string field on another node so that those images can be queried with `gatsby-image`.

### Usage
#### Install
First, install the plugin.

`npm install --save gatsby-plugin-remote-images`

#### Config
Second, set up the `gatsby-config.js` with the plugin.
The most common config would be this:
```javascript
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-remote-images`,
      options: {
        nodeType: 'myNodes',
        imagePath: 'path.to.image',
      },
    },
  ]
}
```
However, you may need more optional config, which is documented here.
```javascript
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-remote-images`,
      options: {
        // The node type that has the images you want to grab.
        // This is generally the camelcased version of the word
        // after the 'all' in GraphQL ie. allMyImages type is myImages
        nodeType: 'myNodes',
        // String that is path to the image you want to use, relative to the node.
        // This uses lodash .get, see docs for accepted formats [here](https://lodash.com/docs/4.17.11#get).
        imagePath: 'path.to.image',
        // ** ALL OPTIONAL BELOW HERE: ** 
        //Name you want to give new image field on the node.
        // Defaults to 'localImage'.
        name: 'theNewImageField',
        // Adds htaccess authentication to the download request if passed in.
        auth: { htaccess_user: `USER`, htaccess_pass: `PASSWORD` },
        // Sets the file extension. Useful for APIs that separate the image file path
        // from its extension. Or for changing the extention.  Defaults to existing
        // file extension.
        ext: ".jpg",
      },
    },
  ]
}
```

### Why?
Why do you need this plugin? The fantastic gatsby-image tool only works on _relative_ paths.  This lets you use it on images from an API with an _absolute_ path.  For example, look at these two response from one GraphQL query:

*Query*
```graphql
allMyNodes {
    edges {
      node {
        id
        imageUrl
      }
    }
  }
```

*Absolute imageUrl NOT available to gatsby-image*
```javascript
allMyNodes: [
  {
    node: {
      id: 123,
      imageUrl: 'http://remoteimage.com/url.jpg'
    }
  }
]
```
*Relative imageUrl IS available to gatsby-image*
```javascript
allMyNodes: [
  {
    node: {
      id: 123,
      imageUrl: 'localImages/url.jpg'
    }
  }
]
```

If you don't control the API that you are hitting (many third party APIs return a field with a string to an absolute path for an image), this means those image aren't run through gatsby-image and you lose all of the benefits.

To get the images and make them avabilable for the above example, follow the install instructions and your config should look like this:

```javascript
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-remote-images`,
      options: {
        nodeType: 'myNodes',
        imagePath: 'imageUrl',
        // OPTIONAL: Name you want to give new image field on the node.
        // Defaults to 'localImage'.
        name: 'allItemImages',
      },
    },
  ]
}
```

Now, if we query `allMyNodes` we can query as we would any gatsby-image node:

```graphql
allMyNodes {
  edges {
    node {
      localImage {
        childImageSharp {
          fluid(maxWidth: 400, maxHeight: 250) {
            ...GatsbyImageSharpFluid
          }
        }
      }
    }
  }
}
```
