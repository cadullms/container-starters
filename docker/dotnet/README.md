To use this from scratch, with dotnet installed locally (otherwise you could as well quickly run a container with it):

1. Make a directory:

   ```sh
   mkdir myapp
   cd myapp
   ```

1. Init a new application:

   ```sh
   dotnet new mvc
   ```

1. Create a file with the name "Dockerfile" in the app's root dir ("myapp" in this case).

1. Enter the content of this [sample dockerfile](https://raw.githubusercontent.com/cadullms/container-starters/master/docker/dotnet/Dockerfile).

1. In the Dockerfile, replace `<name>.dll` with the name of your app (`myapp.dll` in this case).

1. Build the container image:

   ```sh
   docker image build .
   ```

