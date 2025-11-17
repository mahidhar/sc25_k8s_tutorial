# AI and Scientific Research Computing using Kubernetes
AI Examples\
Hands on session

## RAG example using Ollama

Start up the pod:
```
kubectl apply -f ollama-rag.yaml
```
Watch the logs and make sure you wait till the installs are done and the book is downloaded:

```
kubectl logs ollama-username
```
Once the book is downloaded (you will see wget output in the logs), we can get interactive access to the pod and start up the Ollama server and pull the module we want to use (Mistral):

```
kubectl exec -it ollama-username -- /bin/bash
cd /scratch
nohup ollama serve&
ollama pull mistral
```
We can now download our test script and run it:
```
wget https://raw.githubusercontent.com/mahidhar/sc25_k8s_tutorial/refs/heads/main/test.py
python3 -i test.py
```
Now we can run the rag within the interactive python interpreter. Do the following one by one (i.e. wait for results before moving to the next one)
```
rag.invoke("What do you feed pigeons ?")
rag.invoke("Do tame pigeons have better plumage ?")
rag.invoke("What affects pigeon plumage ?")
```

## RAG example using Milvus
### Credit: Mohammad Sada, SDSC

This example demonstrates RAG using Milvus as the vector database instead of ChromaDB. Milvus is a distributed vector database designed for scalable similarity search. In the milvus-rag.yaml file please change the username to a unique name for yourself.

Start up the pod:
```
kubectl apply -f milvus-rag.yaml
```
Watch the logs and make sure you wait till the installs are done:

```
kubectl logs vectordb-example-username -n sc25
```
The pod automatically:
- Installs all Python dependencies
- Installs Ollama
- Starts Ollama server in the background
- Downloads the mistral model in the background

Download the simple example script into the pod once it is running:
```
kubectl exec -it vectordb-example-username -n sc25 -- /bin/bash
cd /scratch
wget https://raw.githubusercontent.com/groundsada/nrp-milvus-example/refs/heads/main/milvus-example.py
```

Once the installation is complete (check logs), you can run the example. \
**Note:** This example connects to the `sc25_milvus` database using credentials from the Kubernetes secret `sc25-milvus-credentials`. The collection name is `simple_rag_example`. You can change the name in the `milvus-example.py` file if you want to create a separate collection of your own. There are two places where the collection name is specified. Look for:

```
 collection_name="simple_rag_example"
```
For all other aspects, the script uses environment variables for Milvus connection, so no manual editing is needed.

```
kubectl exec -it vectordb-example-username -n sc25 -- /bin/bash
cd /scratch
python3 milvus-example.py
```

This simple example:
- Uses a small set of sample documents to demonstrate Milvus vector storage and retrieval
- Shows RAG with Ollama LLM


## End

**Please make sure you did not leave any running pods. Jobs and associated completed pods are OK.**

