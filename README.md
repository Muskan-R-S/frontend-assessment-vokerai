# Frontend Assessment

As a part of our platform we have a UI schema editor that helps users create a [JSON Schema](https://json-schema.org/understanding-json-schema/reference) to describe the structure of their desired response from an llm. See below:

![overview](./images/overview.png)

This is the schema for an object that has two fields:
1. `person_id` is a number
2. `details` is an object that has two fields: `person_name` (string) and `person_age` (number)

Users can delete fields (above), and also change the field type (see below):

![dropdown](./images/dropdown.png)

Your task is to create a JSON Schema editor that allows for 2 levels of nested objects, numbers and string types (enough to make this example) and shows the JSON Schema object they are building up. 

# Criteria

1. Create a UI where users can add fields to the root object
2. Allow users to delete fields
3. Allow users to change the field types: number, string, object
4. Allow users to add nested fields to object fields
5. Allow users to name the fields and add descriptions
6. Display the JSON Schema object you are creating out that reflects the changes the user is making

# Setup

Make sure you have node version 22 installed (minor versions shouldn't matter). If you have a different version of node installed on your computer (check with `node --version`) consider using [nvm](https://github.com/nvm-sh/nvm) to install v22.

Install project dependencies with `npm install`.

To run the development server `npm run dev`.

## Additional

We use [this component library](https://www.shadcn-svelte.com/) and [these icons](https://lucide.dev/icons/) which I've already installed in this repo.
