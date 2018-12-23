# XL Deploy and XL Release: Introducing DevOps as code.
## The developer-first approach to deployment automation

___

Did you ever wish that deploying across multiple data centers and availability zones, pushing updates to hundreds of endpoints, and maintaining environment uniformity was a simple as writing a few lines of code?

Good news.

XL Release and XL Deploy are here.

___

## What is DevOps as code?
At XebiaLabs, we believe that developers shouldn't have to waste their time fiddling with deployments. As coders ourselves, we always wanted deployments and releases to be simple: write code, push it to a repository, and apply it as a part of a CI pipeline. This is why we built XL Deploy and XL Release - two tools that let you automate DevOps as you would anything else. Just define your:

* deployment package,
* infrastructure,
* enviroment,
* release template,
* dashboard,
* and anything else you want

as a YAML file and store it in the same repo where you keep your application code. Time to deploy? Just run a bash script from your terminal or use the XL web GUI.

### XL Deploy

XL Deploy is our deployment automation software. It works with Jenkins, Docker, Puppet, Kubernetes, Chef, Wildfly, and more. Thanks to our model-based approach, automated rollback, and agentless architecture, you can cut down the time you spend on admin, and focus on getting things done. Check out [our docs](https://docs.xebialabs.com/xl-deploy/index.html) to learn more.

### XL Release

XL Release helps you plan, track, and execute releases. Everything is based on templates, so you can automate the boring stuff away and minimize the potential for error. See [our docs](https://docs.xebialabs.com/xl-release/4.5.x/reference_manual.html) for more info.

___

## Try it out yourself
Sounds to good to be true? Seeing is believing, and we believe that trying out the platform will rid you of your doubts. Follow the steps below to install the XL DevOps Platform workshop on your machine and try out its key features.

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
    CONTAINER ID        IMAGE                                           COMMAND                  CREATED             STATUS              PORTS                    NAMES
    b0611ff9ffbb        xebialabsunsupported/rest-o-rant-api            "java -Djava.securit…"   11 seconds ago      Up 10 seconds       8080/tcp                 rest-o-rant-api
    f80c2b00a88e        xebialabsunsupported/xl-release:8.5.0           "/opt/xebialabs/tini…"   3 days ago          Up About an hour    0.0.0.0:5516->5516/tcp   devops-as-code-workshop_xl-release_1
    a99c31ddd458        tecnativa/docker-socket-proxy:latest            "/docker-entrypoint.…"   3 days ago          Up About an hour    2375/tcp                 devops-as-code-workshop_dockerproxy_1
    68a5c6439540        xebialabsunsupported/xl-deploy:8.5.0            "/opt/xebialabs/tini…"   3 days ago          Up About an hour    0.0.0.0:4516->4516/tcp   devops-as-code-workshop_xl-deploy_1
    ```

4. Deploy a more complex package:

    4.1. Import the example package:

    ```bash
    $ xl apply -f exercise-4/rest-o-rant-docker.yaml
    ```

    4.2. Open the [XL Deploy GUI](http://localhost:4516) and deploy version **1.1** of **rest-o-rant-api-docker** and **1.0** of **rest-o-rant-web-docker** to the **Local Docker Engine** environment.

    4.3. Verify that everything works by going to [http://localhost:8181](http://localhost:8181). Type "cow" in the search area to find the "Old Red Cow" restaurant.

### Import a pipeline, export an XL YAML file

1. Open the [XL Deploy GUI](http://localhost:4516) and undeploy **rest-o-rant-api-docker** and **rest-o-rant-web-docker**.

2. Import the pipeline:

    ```bash
    $ xl apply -f exercise-5/rest-o-rant-docker-pipeline.yaml
    ```

    2.1. Open the [XL Release GUI](http://localhost:5516), go to the **Design** tab, click the **REST-o-rant** folder, and go to **Templates**. Review the pipeline you've just imported and compare it to the XL YAML file.

    2.2. Start a new release from that template and follow the instructions.

3. Simplify the pipeline:

    3.1 Open the [XL Deploy GUI](http://localhost:4516), go to **Applications/rest-o-rant-web-docker/1.0**. Set **Undeploy Unused Dependencies** to **true** and add the following to the Application Dependencies map:

    |Key                   |Value|
    |----------------------|-----|
    |rest-o-rant-api-docker|1.1  |

    3.2 Open the [XL Release GUI](http://localhost:5516). Go to the REST-o-rant on Docker pipeline template. Remove the Deploy REST-o-rant application backend task from the first phase and the Undeploy REST-o-rant application backend step from the last phase.

    3.3 Run the pipeline to verify that everything works as intended.

    3.4 Export the XL YAML files for the changes:

    ```bash
    $ xl export -s xl-deploy -p Applications/rest-o-rant-web-docker -f exercise-6/rest-o-rant-web-docker-with-dependencies.yaml
    $ xl export -s xl-release -p REST-o-rant -f exercise-6/rest-o-rant-pipeline-with-dependencies.yaml
    ```

4. Export an XL YAML with deployment artifacts:

    4.1. Open the [XL Deploy GUI](http://localhost:4516). Import **PetClinic-ear/1.0** from the XL Deploy server.

    4.2. Export the XL YAML file for the package to see how to define artifacts:

    ```bash
    $ xl export -s xl-deploy -p Applications/PetClinic-ear/1.0 -f exercise-7/petlinic-ear.yaml
    ```

    4.3. Review the result.