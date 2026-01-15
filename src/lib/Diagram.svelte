<script lang="ts">
	import { onMount } from 'svelte';
	import cytoscape from 'cytoscape';
	import fcose from 'cytoscape-fcose';
	import yaml from 'js-yaml';

	export let yamlContent: string = '';

	let container: HTMLDivElement;
	let cy: any;

	cytoscape.use(fcose);

	function parseYamlToCytoscape(yamlString: string) {
		const elements: any[] = [];
		
		try {
			const data = yaml.load(yamlString) as any;
			if (!data) return elements;

			// Create nodes
			if (data.nodes && Array.isArray(data.nodes)) {
				data.nodes.forEach((node: any) => {
					const cyNode: any = {
						data: {
							id: node.id,
							label: node.name || node.id || 'Unnamed',
							type: node.type || 'system'
						}
					};

					// Set parent if specified
					if (node.parent) {
						cyNode.data.parent = node.parent;
					}

					elements.push(cyNode);
				});
			}

			// Create edges
			if (data.edges && Array.isArray(data.edges)) {
				data.edges.forEach((edge: any, index: number) => {
					elements.push({
						data: {
							id: `edge-${index}`,
							source: edge.source,
							target: edge.target,
							label: edge.label || ''
						}
					});
				});
			}
		} catch (e) {
			console.error('Error parsing YAML:', e);
		}

		return elements;
	}

	function initCytoscape() {
		if (!container || !yamlContent) return;

		const elements = parseYamlToCytoscape(yamlContent);

		if (cy) {
			cy.destroy();
		}

		cy = cytoscape({
			container: container,
			elements: elements,
			style: [
				{
					selector: 'node',
					style: {
						'background-color': '#0084FF',
						'label': 'data(label)',
						'text-valign': 'center',
						'text-halign': 'center',
						'color': '#fff',
						'text-outline-width': 2,
						'text-outline-color': '#0084FF',
						'font-size': '14px',
						'width': 'label',
						'height': 'label',
						'padding': '20px',
						'shape': 'round-rectangle'
					}
				},
				{
					selector: ':parent',
					style: {
						'background-color': '#E8F4FD',
						'background-opacity': 0.8,
						'border-width': 2,
						'border-color': '#0084FF',
						'border-style': 'solid',
						'text-valign': 'top',
						'text-halign': 'center',
						'font-size': '16px',
						'font-weight': 'bold',
						'color': '#333',
						'text-outline-width': 0,
						'padding': '30px'
					}
				},
				{
					selector: 'edge',
					style: {
						'width': 2,
						'line-color': '#999',
						'target-arrow-color': '#999',
						'target-arrow-shape': 'triangle',
						'curve-style': 'bezier',
						'label': 'data(label)',
						'text-rotation': 'autorotate',
						'font-size': '12px',
						'color': '#666',
						'text-outline-width': 2,
						'text-outline-color': '#fff'
					}
				}
			],
			layout: {
				name: 'fcose',
				quality: 'proof',
				animate: false,
				fit: true,
				padding: 50,
				nodeDimensionsIncludeLabels: true,
				uniformNodeDimensions: false,
				packComponents: true,
				nodeRepulsion: 8000,
				idealEdgeLength: 100,
				edgeElasticity: 0.45,
				nestingFactor: 0.1,
				numIter: 2500,
				tile: true,
				tilingPaddingVertical: 40,
				tilingPaddingHorizontal: 40,
				gravity: 0.25,
				gravityRange: 3.8,
				gravityCompound: 1.0,
				gravityRangeCompound: 1.5,
				initialEnergyOnIncremental: 0.3
			}
		});
	}

	onMount(() => {
		initCytoscape();
		return () => {
			if (cy) {
				cy.destroy();
			}
		};
	});

	$: if (yamlContent) {
		initCytoscape();
	}
</script>

<div bind:this={container} class="diagram-container"></div>

<style>
	.diagram-container {
		width: 100%;
		height: 100%;
		background: #f9f9f9;
		position: relative;
	}
</style>
