# Stage 1: Build the application
FROM node:18 AS build

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm install

COPY . .

RUN npm run build

# Stage 2: Run the application
FROM node:18

WORKDIR /app

COPY --from=build /app /app

RUN npm install

EXPOSE 3000

CMD ["npm", "start"]	