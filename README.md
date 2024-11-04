# Blog Setup Guide

This guide will walk you through the process of cloning and setting up the blog repository, installing a theme, and running the blog locally using Hugo.

## Prerequisites

- [Git](https://git-scm.com/downloads)
- [Hugo](https://gohugo.io/getting-started/installing/)

## Setup Instructions

1. **Clone the Blog Repository**  
   Start by cloning the main blog repository to your local machine.

   ```
   git clone https://github.com/ByteShifters/Blog
   ```

2. **Navigate to the `themes` Directory**  
   Move into the `themes` directory inside the `Blog` project.

   ```
   cd Blog/themes
   ```

3. **Clone the Theme Repository**  
   Clone the Hugo theme into the `themes` directory and rename it as `ByteShifter`.

   ```
   git clone https://github.com/ByteShifters/Hugo-Theme.git ByteShifter
   ```

4. **Navigate Back to the Root Directory**  
   Move back to the root directory of the blog project.

   ```
   cd ..
   ```

5. **Run the Blog**  
   Use Hugo's development server to start the blog locally.

   ```
   hugo serve
   ```

## Accessing Your Local Blog

Once the server is running, you should see an output like this:

```plaintext
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop the server.
```

Navigate to `http://localhost:1313/` in your web browser to see your blog in action.

