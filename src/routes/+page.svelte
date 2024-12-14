<script lang="ts">
    import { Input } from '$lib/components/ui/input';
    import { Button } from "$lib/components/ui/button";
    import * as Select from "$lib/components/ui/select";
    import * as DropdownMenu from "$lib/components/ui/dropdown-menu";
    import { writable } from "svelte/store";
    import { Hash, CaseUpper, Braces } from 'lucide-svelte';

    // Primary structure 
    interface Properties {
        type: string;
        name: string;
        description: string;
        nestedFields?: { [key: string]: Properties };
    }

    let root = writable<{ [key: string]: Properties }>({}); //root element
    let selectedType = writable('Type'); 
    let name = writable('');
    let description = writable('');
    let editingKey = writable('');
    let nestedFieldName = writable('');
    let nestedFieldDescription = writable('');
    let selectedNestedType = writable('Type');

    function add_field(type_input: string, name_input: string, description_input: string) {
        console.log("In add field")
        console.log(type_input)
        const key = $editingKey || name_input + type_input;
        const new_entry = { 
            type: type_input, 
            name: name_input, 
            description: description_input };

        console.log(key)
        root.update(current => ({
            ...current,
            [key]: new_entry
        }));

        // Resetting values
        selectedType.set('Type');
        name.set('');
        description.set('');
        editingKey.set('');
    }

    function add_nested_field(parentKey: string, type: string, name: string, description: string) {
        console.log("######## here here here ###########")
        root.update(current => {
            const parent = current[parentKey];
            if (!parent.nestedFields) {
                parent.nestedFields = {};
            }
            const nestedKey = name + type;
            parent.nestedFields[nestedKey] = { type, name, description };
            return current;
        });

        // Reset values
        nestedFieldName.set('');
        nestedFieldDescription.set('');
        selectedNestedType.set('Type');
    }

    function delete_field(key: string) {
        root.update(current => {
            const { [key]: _, ...rest } = current;
            return rest;
        });
    }

    function delete_nested_field(parentKey: string, nestedKey: string) {
        console.log("this is parent  key",{parentKey})
        root.update(current => {
            const parent = current[parentKey];
            if (parent?.nestedFields) {
                delete parent.nestedFields[nestedKey];
            }
            return current;
        });
    }

    // to set the values
    function edit_field(key: string) {
        const field = $root[key];
        if (field) {
            name.set(field.name);
            description.set(field.description);
            selectedType.set(field.type);
            editingKey.set(key);
        }
    }

</script>

<div class="flex justify-center">
    <div class="grid grid-cols-2 mt-10 w-full max-w-xxl ml-20">
        <div>
            <div class="centered">
                <!-- These are the Existing fields -->
                <ul class="padding">
                    {#each Object.entries($root) as [key, field]}
                        <li >
                            <div class="line margin" >
                                <Input placeholder={field.type} value={field.type} disabled />
                                <Input placeholder={field.name} value={field.name} disabled />
                                <Input placeholder={field.description} value={field.description} disabled />
                                <Button on:click={() => edit_field(key)}>Edit</Button>
                                <Button on:click={() => delete_field(key)}>Delete</Button>
                            </div>

                            <!-- Only display nested fields if type is object -->
                            {#if field.type === 'Object'}
                                <div class="nested-fields">
                                    <h4>Nested Fields for "{field.name}"</h4>
                                    {#if field.nestedFields}
                                        <ul>
                                            {#each Object.entries(field.nestedFields) as [nestedKey, nestedField]}
                                                <li class="line">
                                                    <Input placeholder={nestedField.type} value={nestedField.type} disabled />
                                                    <Input placeholder={nestedField.name} value={nestedField.name} disabled />
                                                    <Input placeholder={nestedField.description} value={nestedField.description} disabled />
                                                    <Button on:click={() => delete_nested_field(key, nestedKey)}>Delete</Button>
                                                </li>
                                            {/each}
                                        </ul>
                                    {/if}

                                    <!-- Add Nested Field Form -->
                                    <div class="nested-add-form line">
                                        <Select.Root portal={null}>
                                            <Select.Trigger>
                                                <Select.Value placeholder="Select Type" />
                                            </Select.Trigger>
                                            <Select.Content>
                                                <Select.Group>
                                                    <Select.Label>Type</Select.Label>
                                                    <Select.Separator />
                                                    <Select.Item value="Number" on:click={() => selectedNestedType.set('Number')}><Hash class="icon" /> Number</Select.Item>
                                                    <Select.Item value="String" on:click={() => selectedNestedType.set('String')}><CaseUpper class="icon" /> String</Select.Item>
                                                    <Select.Item value="Object" on:click={() => selectedNestedType.set('Object')}><Braces class="icon" /> Object</Select.Item>
                                                </Select.Group>
                                            </Select.Content>
                                        </Select.Root>
                                        <Input bind:value={$nestedFieldName} placeholder="Nested Field Name" />
                                        <Input bind:value={$nestedFieldDescription} placeholder="Nested Field Description" />
                                        <Button on:click={() => add_nested_field(key, $selectedNestedType, $nestedFieldName, $nestedFieldDescription)}> 
                                            Add Nested Field</Button>
                                    </div>
                                </div>
                            {/if}
                        </li>
                    {/each}
                </ul>

                <!-- Main Add/Edit Form -->
                <form onsubmit={() => add_field($selectedType, $name, $description)}>

                    <div class="line">
                        <Select.Root portal={null}>
                            <Select.Trigger>
                                <Select.Value placeholder="Select Type" />
                            </Select.Trigger>
                            <Select.Content>
                            <Select.Group>
                                <Select.Label>Type</Select.Label>
                                <Select.Separator />
                                <Select.Item value="Number" on:click={() => selectedType.set('Number')}><Hash class="icon" /> Number</Select.Item>
                                <Select.Item value="String" on:click={() => selectedType.set('String')}><CaseUpper class="icon" /> String</Select.Item>
                                <Select.Item value="Object" on:click={() => selectedType.set('Object')}><Braces class="icon" /> Object</Select.Item>
                            </Select.Group>
                            </Select.Content>
                        </Select.Root>
                        <Input bind:value={$name} placeholder="Name" />
                        <Input bind:value={$description} placeholder="Description" />
                        <Button type="submit">{$editingKey ? 'Update' : 'Add'}</Button>
                    </div>
                </form>
            </div>
        </div>

        <!-- JSON Schema Display -->
        <div>
            <div class="padding"> 
                <h2>JSON Schema:</h2>
                <pre>{"{"}</pre>
                {#each Object.entries($root) as [key, field] }
                    <pre>{JSON.stringify(field, null, 2)}</pre>
                    <p>,</p>
                {/each}
                <pre>{"}"}</pre>
                
            </div>
        </div>
    </div>
</div>

<style>
    .line {
        display: flex;
        gap: 2%;
        align-items: center;
    }
    .nested-fields {
        margin-left: 2rem;
        border-left: 2px solid #ddd;
        padding-left: 1rem;
    }
    .nested-add-form {
        margin-top: 1rem;
    }
    .padding {
        padding: 5%;
    }
    .margin {
        margin: 5%;
    }
    .myStyle {
        font-size: 0.875rem;
        line-height: 1.25rem;
        color: hsla(220, 2%, 30%, 0.799);
        padding: 0.5rem;
        border: 1px solid hsl(214.3 31.8% 91.4% / 1);
        border-radius: 0.25rem;
        display: flex;
        align-items: center;
        gap: 0.5rem;
        cursor: pointer;
    }
</style>