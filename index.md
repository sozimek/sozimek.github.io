# XL Release and XL Deploy: Introducing DevOps as code.
# The developer-first approach to infrastructure and deployments
___

Did you ever wish that reaching five-nines availability, deploying across multiple data centers and availability zones, and pushing updates to hundreds of endpoints was a simple as writing a few lines of code?

Good news.

XL Release and XL Deploy are here.

___

## What is DevOps as code?
At XebiaLabs, we believe that developers shouldn't have to waste their time fiddling with deployments. As coders ourselves, we always wanted deployments and releases to be simple: write code, push it to a repository, and apply it as a part of a CI pipeline. This is why we built XL Release and XL Deploy - two tools that let you automate DevOps as you would anything else. Just define your:

* deployment package,
* infrastructure,
* enviroment,
* release template,
* dashboard,
* and anything else you want

as a YAML file and store it in the same repo where you keep your application code. Time to deploy? Just run a bash script from your terminal or use the XL web GUI.

___

## Try it out yourself
Sounds to good to be true? Seeing is believing, and we believe that trying out the platform will rid you of your doubts. Follow the steps below to install the XL DevOps Platform workshop on your machine.

### Set things up

1. Download the workshop.

    ```bash
    $ curl -LO https://github.com/xebialabs/devops-as-code-workshop/archive/master.zip
    $ unzip master.zip
    $ cd devops-as-code-workshop-master
    ```

2. Get [XL Deploy](https://xebialabs.com/products/xl-deploy/trial/) and [XL Release](https://xebialabs.com/products/xl-release/trial/) licenses and copy them to `docker/xl-deploy/default-conf/deployit-license.lic`. 

3. Start the platform.

    ```bash
    $ docker-compose up --build
    devops-as-code-workshop_xl-cli_1 exited with code 0
    ```

4. Open the XL Deploy GUI at [http://localhost:4516/](http://localhost:4516) and the XL Release GUI at [http://localhost:5516/](http://localhost:5516/). The password and login for both are `admin`.

5. Install the XL CLI.

    *On Linux:*
    ```bash
    $ curl -LO https://s3.amazonaws.com/xl-cli/bin/8.5.0/linux-amd64/xl
    $ chmod +x xl
    $ sudo mv xl /usr/local/bin
    ```

    *On macOS:*
    ```bash
    $ curl -LO https://s3.amazonaws.com/xl-cli/bin/8.5.0/darwin-amd64/xl
    $ chmod +x xl
    $ sudo mv xl /usr/local/bin
    ```

6. Verify that everything works:

	 ```bash
	 $ xl help
	 ```

### Perform an example deployment

1. Open the XL Deploy GUI at [http://localhost:4516/](http://localhost:4516) (thee password and login are `admin`). In the **Infrastructure** tree, go to the **local-docker** entry and run the **Check Connection** task.

2. Set up the environment:

    ```bash
	$ cd devops-as-code-workshop-master
    $ xl apply -f exercise-2/docker-environment.yaml
    ```


3. Deploy a simple package:

    3.1. Import the YAML package

    ```bash
    $ xl apply -f exercise-3/rest-o-rant-api-docker.yaml
    ```
    
    3.2. Open the [XL Deploy GUI](http://localhost:4516) and deploy the **rest-o-rant-api-docker** application to the **Local Docker Engine** enrivonment.

    3.3. Verify that the application is running:

    ```bash
    $ docker ps
    ```

4. Deploy a more complex package:

    4.1. Import the example package:

         ```bash
         $ xl apply -f exercise-4/rest-o-rant-docker.yaml
         ```

    4.2. Open the [XL Deploy GUI](http://localhost:4516) and deploy version **1.1** of **rest-o-rant-api-docker** and **1.0** of **rest-o-rant-web-docker** to the **Local Docker Engine** environment.

    4.3. Verify that everything works by going to [http://localhost:8181](http://localhost:8181). Type "cow" in the search area to find the "Old Red Cow" restaurant.

### Import a pipeline, export an XL YAML file
