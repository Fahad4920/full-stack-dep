# # Use Node.js image with a newer version
# FROM node:18

# # Set the working directory
# WORKDIR /usr/src/app

# # Copy package files
# COPY package*.json ./

# # Install dependencies
# RUN npm install

# # Copy the rest of the app
# COPY . .

# # Expose port 3000
# EXPOSE 3000

# # Start the React app
# CMD ["npm", "start"]








# Use Node.js for development
FROM node:18

# Set the working directory
WORKDIR /usr/src/app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install
# RUN npm install react-router
RUN npm install react-router-dom@6


# Copy the entire project
COPY . .

# Expose Vite's default development server port
EXPOSE 5173

# Start the Vite dev server
CMD ["npm", "run", "dev", "--", "--host"]
