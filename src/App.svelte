<script lang="ts">
  import Diagram from './lib/Diagram.svelte';
  
  let yamlContent = $state(`# Simple FEAD Diagram Example
nodes:
  - id: system1
    name: "E-Commerce System"
    type: system
  
  - id: web_app
    name: "Web Application"
    type: container
    parent: system1
  
  - id: api
    name: "REST API"
    type: container
    parent: system1
  
  - id: database
    name: "Database"
    type: container
    parent: system1
  
  - id: auth_component
    name: "Authentication"
    type: component
    parent: api
  
  - id: product_component
    name: "Product Service"
    type: component
    parent: api

  - id: user_system
    name: "User Management"
    type: system

edges:
  - source: web_app
    target: api
    label: "HTTPS/JSON"
  
  - source: api
    target: database
    label: "SQL"
  
  - source: web_app
    target: user_system
    label: "Authenticates"
  
  - source: auth_component
    target: product_component
    label: "Validates"`);
</script>

<main>
  <div class="container">
    <div class="editor-panel">
      <h2>YAML Editor</h2>
      <textarea 
        bind:value={yamlContent}
        placeholder="Enter your FEAD diagram YAML here..."
        spellcheck="false"
      ></textarea>
    </div>
    
    <div class="preview-panel">
      <h2>Diagram Preview</h2>
      <div class="diagram-wrapper">
        <Diagram yamlContent={yamlContent} />
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, sans-serif;
    background: #f3f4f6;
  }

  main {
    height: 100vh;
    display: flex;
    flex-direction: column;
  }

  .container {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
    height: 100vh;
    padding: 20px;
    box-sizing: border-box;
  }

  .editor-panel,
  .preview-panel {
    display: flex;
    flex-direction: column;
    background: white;
    border-radius: 8px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
    padding: 20px;
    overflow: hidden;
  }

  h2 {
    margin: 0 0 15px 0;
    color: #1f2937;
    font-size: 18px;
    font-weight: 600;
  }

  textarea {
    flex: 1;
    width: 100%;
    padding: 15px;
    border: 1px solid #e5e7eb;
    border-radius: 4px;
    font-family: 'Courier New', Courier, monospace;
    font-size: 14px;
    resize: none;
    box-sizing: border-box;
    background: #f9fafb;
    color: #1f2937;
    line-height: 1.5;
  }

  textarea:focus {
    outline: none;
    border-color: #6366f1;
    background: white;
  }

  .diagram-wrapper {
    flex: 1;
    position: relative;
    overflow: hidden;
    border: 1px solid #e5e7eb;
    border-radius: 4px;
  }
</style>
