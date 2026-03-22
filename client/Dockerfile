ARG NODE_VERSION=16-buster-slim
ARG NGINX_VERSION=alpine


# Use node image for base image for all stages.
FROM node:${NODE_VERSION} AS builder

# Set working directory for all build stages.
WORKDIR /app

COPY package.json package-lock.json* ./

RUN --mount=type=cache,target=/root/.npm npm ci

# Copy the rest of the source files into the image.
COPY . .

RUN mv .env.example .env

# Run the build script.
RUN npm run build

## stage 2
FROM nginxinc/nginx-unprivileged:${NGINX_VERSION} AS runner

COPY nginx.conf /etc/nginx/nginx.conf

COPY --chown=nginx:nginx --from=builder /app/dist /usr/share/nginx/html

USER nginx

EXPOSE 8080

ENTRYPOINT ["nginx", "-c", "/etc/nginx/nginx.conf"]
CMD ["-g", "daemon off;"]