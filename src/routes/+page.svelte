<script lang="ts">
    // This is the JSON Schema for an object such as: {name: 'John', age: 18}
    let jsonSchema = {
        "type": "object",
        "properties": {
            "name": {"type": "string"},
            "age": {"type": "number"}
        }
    }
</script>

<div class="flex justify-center">
    <div class="grid grid-cols-2 mt-10 w-full max-w-lg">
        <div>
            <!-- TODO: make an editor to add and edit fields -->
        </div>
        <div>
            <!-- TODO: show the jsonSchema raw value here -->
        </div>
    </div>
</div>
