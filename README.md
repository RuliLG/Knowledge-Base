# Knowledge Database

## Requirements

- Docker
- Docker Compose

## How to run

- Clone this repository
- Create an `.env` file. You can copy the contents of `.env.example` and make any necessary changes
- Run `docker-compose build` to build the image
- Run `docker-compose up -d` to run the containers

## Endpoints

### `POST` `/insert`

Insert new knowledge into the database. The body must be a JSON with the following structure:

```json
{
    "data": [
        {
            "id": "08c8ded7-49cc-4746-b2eb-6330c811b7a9",
            "text": "Text that will be converted to embeddings",
            "entity": "Name of the entity that will be used to filter if needed",
            "payload": {
                "key": "optional"
            }
        }
    ]
}
```

### `GET` `/query`

This endpoint can receive the following parameters:

- `query`: Query to search for
- `k`: Number of results to return. Default = 5
- `entities`: Comma separated list of entities to filter the results. Default = all entities
- `where`: A JSON string with the conditions to filter the results. The keys are the keys of the payload and the values should be a comma separated string with the values to filter. For example: `{"key": "value1,value2"}`

### `DELETE` `/delete/{id}`

Delete a knowledge from the database. The `id` parameter is the id of the knowledge to delete.

## Vector stores

This is the base of the knowledge database. They store the embeddings of the knowledge and are used to retrieve the most similar knowledge to a query. The following vector store providers are available under the `VECTOR_STORE` env variable:

- `qdrant`: Uses [Qdrant](https://qdrant.tech/) as the vector store. You can provide a custom host and port under the `QDRANT_HOST` and `QDRANT_PORT` env variables

## Embeddings

The following embedding model providers are available under the `EMBEDDING_PROVIDER` env variable:

- `openai`: Use OpenAI's embedding model. You need to provide an API key in the `OPENAI_API_KEY` env variable
- `huggingface`: Use open source embedding models from HuggingFace

In every case, provide the name of the model in the `EMBEDDINGS_MODEL_NAME` env variable.

## Reranking

After the results are retrieved from the database, they are reranked using [https://github.com/PrithivirajDamodaran/FlashRank](FlashRank). You can use the `RERANKING_MODEL_NAME` env variable to change the model used for reranking:

- `ms-marco-TinyBERT-L-2-v2`: Default model
- `ms-marco-MiniLM-L-12-v2`
- `rank-T5-flan`: Best results but slower
- `ms-marco-MultiBERT-L-12`: Supports 100+ languages
- `ce-esci-MiniLM-L12-v2`
- `rank_zephyr_7b_v1_full`: Offers very competitive performance, with large context window and relatively faster for a 4GB model. Max 20 passages in the `MAX_K` env variable (default 1000)
