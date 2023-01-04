# This is the base dockerfile. Here the base image is pulled and the ras setup is done for the project.
# Make sure to include the base setup for lerna here.
FROM node:16 as base
WORKDIR /app
COPY ./package.json ./
RUN npm install
COPY ./lerna.json ./
# Package footer
FROM base as footer-build
WORKDIR /app/packages/footer
# Here the dependencies will be installed and the local required packages bootstrapped.
# The --slim flag will cause the package json to only include the dependencies, so not all changes to the package json cause docker to reinstall all packages.
COPY  packages/footer/package-slim.json package.json
WORKDIR /app/
RUN npx lerna bootstrap --scope=footer --includeDependencies
WORKDIR /app/packages/footer
# The normal package.json should be copied after the install into the container
COPY  packages/footer/package.json ./
COPY  packages/footer/ ./
# This will only add the command to the dockerfile if the build script exists in the package otherwise its ignored.
RUN npm run build
# Package header
FROM base as header-build
WORKDIR /app/packages/header
# Here the dependencies will be installed and the local required packages bootstrapped.
# The --slim flag will cause the package json to only include the dependencies, so not all changes to the package json cause docker to reinstall all packages.
COPY  packages/header/package-slim.json package.json
WORKDIR /app/
RUN npx lerna bootstrap --scope=header --includeDependencies
WORKDIR /app/packages/header
# The normal package.json should be copied after the install into the container
COPY  packages/header/package.json ./
COPY  packages/header/ ./
# This will only add the command to the dockerfile if the build script exists in the package otherwise its ignored.
RUN npm run build
# Package remixapp
FROM base as remixapp-build
WORKDIR /app/packages/remixapp
# Here the dependencies will be installed and the local required packages bootstrapped.
# The --slim flag will cause the package json to only include the dependencies, so not all changes to the package json cause docker to reinstall all packages.
COPY  packages/remixapp/package-slim.json package.json
COPY  packages/remixapp/ ./
WORKDIR /app/
COPY --from=header-build /app/packages/header/package.json /app/packages/header/
COPY --from=footer-build /app/packages/footer/package.json /app/packages/footer/
RUN npx lerna bootstrap --scope=remixapp --includeDependencies
COPY --from=header-build /app/packages/header/ /app/packages/header/
COPY --from=footer-build /app/packages/footer/ /app/packages/footer/
WORKDIR /app/packages/remixapp
# The normal package.json should be copied after the install into the container
COPY  packages/remixapp/package.json ./
# This will only add the command to the dockerfile if the build script exists in the package otherwise its ignored.
RUN npm run build
ENV NODE_ENV=production
CMD ["npm", "run", "start"] # will launch the remix app when we run this Docker image.
