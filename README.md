# modifixer
Modifixer is a powerful browser tool designed for inspecting HTML elements, editing their attributes and styles, and managing global variables in real-time. It provides an intuitive, user-friendly interface for web developers and ethical hackers to interact with websites directly through the browser's console.

ONLY FOR ETHICAL USAGE

Features:

  - Inspect Mode: Activate to hover over and click on elements to view and modify their attributes and styles.
  - Element Editing: Modify HTML element attributes and CSS styles on the fly, with immediate changes visible on the page.
  - Global Variable Management: View and edit global JavaScript variables in the current window, with real-time updates applied directly.
  - Responsive Interface: A well-designed, sleek container with a smooth and interactive experience, built using JavaScript and CSS.
  - Fully Interactive: Inspect, edit, and apply changes to elements and variables seamlessly.

Installation:

  - Copy the Modifixer script or include it in the developer console.
  - Launch the script in your browser to activate Modifixer.
  - Use the on-screen buttons to toggle between inspecting elements, editing global variables, or closing the interface.
If it doesn't work just copy this:
(function Modifixer() {
    let inspectModeActive = false;
    let hoverElement = null;

    // Create main container
    const container = document.createElement('div');
    container.style.cssText = `
        position: fixed;
        top: 5%;
        left: 5%;
        width: 90%;
        height: 90%;
        background: #1e1e2d;
        color: white;
        z-index: 99999;
        border-radius: 10px;
        font-family: Arial, sans-serif;
        box-shadow: 0 0 20px black;
        overflow: hidden;
        display: flex;
        flex-direction: column;
    `;

    // Header Section
    const header = document.createElement('div');
    header.style.cssText = `
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 10px;
        background: #2c2c3d;
        border-bottom: 2px solid #4caf50;
    `;
    header.innerHTML = `
        <h2 style="margin: 0; font-size: 1.5rem; color: #4caf50;">Modifixer</h2>
        <div>
            <button id="modifixer-inspect" style="background: #4caf50; color: white; border: none; padding: 5px 15px; border-radius: 5px; cursor: pointer;">Inspect Mode</button>
            <button id="modifixer-edit-vars" style="background: #4caf50; color: white; border: none; padding: 5px 15px; border-radius: 5px; cursor: pointer;">Edit All Variables</button>
            <button id="modifixer-close" style="background: red; color: white; border: none; padding: 5px 15px; border-radius: 5px; cursor: pointer;">Close</button>
        </div>
    `;

    // Content Section
    const content = document.createElement('div');
    content.style.cssText = `
        flex: 1;
        padding: 20px;
        overflow-y: auto;
        background: #1e1e2d;
    `;
    content.innerHTML = `<p style="color: #aaa;">Welcome to Modifixer! Use the buttons above to inspect elements or edit global variables.</p>`;

    // Add elements to container
    container.appendChild(header);
    container.appendChild(content);

    // Add container to body
    document.body.appendChild(container);

    // Close Button
    header.querySelector('#modifixer-close').addEventListener('click', () => {
        deactivateInspectMode();
        document.body.removeChild(container);
    });

    // Inspect Button
    header.querySelector('#modifixer-inspect').addEventListener('click', activateInspectMode);

    // Edit Variables Button
    header.querySelector('#modifixer-edit-vars').addEventListener('click', showAllVariables);

    // Activate Inspect Mode
    function activateInspectMode() {
        inspectModeActive = true;
        container.style.display = 'none';
        document.body.addEventListener('mousemove', onHover);
        document.body.addEventListener('click', onClickElement, true);
    }

    // Deactivate Inspect Mode
    function deactivateInspectMode() {
        inspectModeActive = false;
        hoverElement = null;
        removeHighlight();
        container.style.display = 'flex';
        document.body.removeEventListener('mousemove', onHover);
        document.body.removeEventListener('click', onClickElement, true);
    }

    // Highlight hovered elements
    function onHover(e) {
        if (!inspectModeActive) return;
        if (hoverElement) hoverElement.style.outline = '';
        const element = e.target;
        hoverElement = element;
        element.style.outline = '2px solid #FFD700';
    }

    // Remove highlights
    function removeHighlight() {
        if (hoverElement) hoverElement.style.outline = '';
    }

    // Click to Inspect Element
    function onClickElement(e) {
        if (!inspectModeActive) return;
        e.preventDefault();
        e.stopPropagation();
        const element = e.target;
        deactivateInspectMode();
        removeHighlight();
        displayElementDetails(element);
    }

    // Display Element Details and Editing Options
    function displayElementDetails(element) {
        container.style.display = 'flex';
        content.innerHTML = `
            <h3>Inspecting: ${element.tagName}</h3>
            <h4>Attributes:</h4>
            <ul>${Array.from(element.attributes || [])
                .map(attr => `
                    <li>${attr.name}: 
                    <input value="${attr.value}" id="attr-${attr.name}" style="background: #333; color: #fff; border: none; padding: 5px; border-radius: 5px;"></li>`)
                .join('') || '<li>None</li>'}</ul>
            <button id="apply-attributes" style="margin: 10px 0; padding: 10px; background: #4caf50; color: white; border: none; border-radius: 5px; cursor: pointer;">Apply Attributes</button>
            <h4>Styles:</h4>
            <ul>${element.style.cssText
                .split(';')
                .filter(style => style.trim())
                .map(style => {
                    const [key, value] = style.trim().split(':');
                    return `<li>${key}: 
                    <input value="${value.trim()}" id="style-${key.trim()}" style="background: #333; color: #fff; border: none; padding: 5px; border-radius: 5px;"></li>`;
                })
                .join('') || '<li>None</li>'}</ul>
            <button id="apply-styles" style="margin: 10px 0; padding: 10px; background: #4caf50; color: white; border: none; border-radius: 5px; cursor: pointer;">Apply Styles</button>
            <button id="exit-inspect" style="margin-top: 20px; padding: 10px; background: #4caf50; color: white; border: none; border-radius: 5px; cursor: pointer;">Return to Inspect Mode</button>
        `;
        document.getElementById('apply-attributes').addEventListener('click', () => {
            Array.from(element.attributes || []).forEach(attr => {
                const newValue = document.getElementById(`attr-${attr.name}`).value;
                element.setAttribute(attr.name, newValue);
            });
        });
        document.getElementById('apply-styles').addEventListener('click', () => {
            const newStyles = element.style.cssText
                .split(';')
                .filter(style => style.trim())
                .map(style => style.trim().split(':'))
                .forEach(([key]) => {
                    const newValue = document.getElementById(`style-${key.trim()}`).value;
                    element.style[key.trim()] = newValue;
                });
        });
        document.getElementById('exit-inspect').addEventListener('click', activateInspectMode);
    }

    // Display All Variables
    function showAllVariables() {
        deactivateInspectMode();
        content.innerHTML = `<h3>All Global Variables:</h3>`;
        const vars = Object.entries(window).filter(([key, value]) => typeof value !== 'function');
        vars.forEach(([key, value]) => {
            const div = document.createElement('div');
            div.style.cssText = 'margin-bottom: 10px; color: #aaa;';
            div.innerHTML = `
                <strong>${key}</strong>: 
                <input id="var-${key}" value="${value}" style="width: 70%; background: #333; color: #fff; border: none; padding: 5px; border-radius: 5px;">
                <button style="margin-left: 5px; background: #4caf50; color: white; border: none; padding: 5px; border-radius: 5px; cursor: pointer;" onclick="window['${key}'] = document.getElementById('var-${key}').value;">Apply</button>
            `;
            content.appendChild(div);
        });
    }
})();

Screenshots:
![grafik](https://github.com/user-attachments/assets/a5717440-3eea-46d2-843c-fe8298848e37)
![grafik](https://github.com/user-attachments/assets/f773b553-b602-4fc1-9dca-daaaf8a41876)

Usage:

  - Inspect Mode: Click the "Inspect Mode" button to start hovering over and inspecting elements.
  - Edit Variables: View and modify global JavaScript variables directly via the "Edit All Variables" button.
  - Close: Exit the interface or deactivate inspect mode by clicking the close button.
