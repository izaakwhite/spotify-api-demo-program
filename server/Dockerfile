FROM node:current AS base

WORKDIR /server

COPY package.json package-lock.json* .env server.js ./

RUN \
    if [ -f package-lock.json ]; then npm ci; \ 
    else echo "Warning: Lockfile not found. It is recommended to commit lockfiles to version control." && npm install; \
    fi

RUN npm run build; 

CMD ["npm", "start"]