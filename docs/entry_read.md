# Entry Read

The EntryReadCL class is responsible for managing the READ (GET) operation through the OData V2 model. This class eliminates the need for developers to concern themselves with fragments, user input validations, and value help creation when working on custom SAPUI5 applications or Fiori Elements extensions. The following section outlines the key features of the Entry Read class.

## Features

[DIALOG_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.Dialog
[SIMPLEFORM_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.layout.form.SimpleForm
[SMARTFORM_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.comp.smartform.SmartForm
[OBJECT_PAGE_URL]: https://sapui5.hana.ondemand.com/#/api/sap.uxap.ObjectPageLayout
[ENTRY_CREATE_URL]: ./entry_create.md
[ENTRY_UPDATE_URL]: ./entry_update.md
[ENTRY_DELETE_URL]: ./entry_delete.md

- [sap.m.Dialog][DIALOG_URL] generation with a [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL], or custom content
- [sap.uxap.ObjectPageLayout][OBJECT_PAGE_URL] generation with a [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL], or custom content
- Property sorting, excluding
- Label generation for the [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL] elements
- Call a fragment and bind the context in case you do not want to use the auto-generated dialog

!!! danger "Attention"

    Please be advised that the majority of features available in the [Entry Create][ENTRY_CREATE_URL] class are also accessible in the [Entry Read](#entry-read) class. However, please note that the original documentation was created on the [Entry Create][ENTRY_CREATE_URL] page. To view a list of available features, please refer to the [Available Features](#available-features) section.

## Use Case

Let's say you have an `EntitySet` named **Products** that is bound to a table, and the **Products** entity has many properties that don't fit in the table. The goal is to allow the end user to select a row from the table and display the details with more properties through the OData V2 Model on a dialog screen. The following steps outline the process.

1) It is necessary to create a **.fragment.xml** file that contains a dialog with form content (Simple, Smart, etc.) and call it from the controller or generate the dialog directly on the controller.

2) It is necessary to handle the selection of the table.

3) It is necessary to handle the binding of the selected row to a dialog or a form.

The [EntryReadCL](#entry-read) class is responsible for executing all of the steps mentioned above.

## Constructor

[CONTROLLER_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.core.mvc.Controller

In order to utilise the functionality of EntryReadCL, it is necessary to initialise the object.

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Mandatory</th>
      <th>Default Value</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>controller</td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.mvc.Controller">sap.ui.core.mvc.Controller</a></td>
      <td>Yes</td>
      <td></td>
      <td>The controller object (usually <code>this</code> object)</td>
    </tr>
    <tr>
      <td>settings</td>
      <td><code>object</code></td>
      <td>Yes</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>&emsp;entityPath</td>
      <td><code>string</code></td>
      <td>Yes</td>
      <td></td>
      <td>The name of the <strong>EntitySet</strong>. It can start with a <strong>"/"</strong> (slash)</td>
    </tr>
    <tr>
      <td>&emsp;initializer</td>
      <td><code>string</code> | <a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Context">sap.ui.model.Context</a> | <code>EntityKeysT</code></td>
      <td>Yes</td>
      <td></td>
      <td>This parameter identifies the table ID, context binding, or key values of the entity that will be displayed.</td>
    </tr>
    <tr>
      <td>modelName?</td>
      <td><code>string</code></td>
      <td>No</td>
      <td><code>undefined</code></td>
      <td>The name of the OData V2 model which can be found on the manifest.json file. <strong>Leave this parameter undefined</strong> if the name of the OData model = "" (empty string)</td>
    </tr>
  </tbody>
</table>

There are three distinct methods for constructing an object from the [Entry Read](#entry-read) class.

### Constructor with a Table ID

[MESSAGEBOX_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.MessageBox

The most straightforward method for utilizing the capabilities of the [Entry Read](#entry-read) class is to construct an object with the ID of a table that you have on your XML view. This method offers several advantages.

1) The table row selected by the end user is automatically detected by the [Entry Read](#entry-read) class, and the context binding of the selected row will be bound to the auto-generated dialog.

2) If no table row is selected by the end user, a default message is displayed in the [sap.m.MessageBox.error][MESSAGEBOX_URL] to the end user.

!!! danger "Attention"

    Please note that this method supports only the table types and selection modes listed below. In the event that the selection mode of the table whose ID is being used for object construction is not supported, an error will be thrown by the library.

!!! tip

    The default message displayed when the end user has not selected a row from the table yet can be modified using the [setSelectRowMessage()](#select-row-message) method.

#### Supported Table Types

<table>
  <thead>
    <tr>
      <th>Table Type</th>
      <th>Selection Mode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.Table">sap.m.Table</a></td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ListMode">SingleSelect</a> | <a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ListMode">SingleSelectLeft</a> | <a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ListMode">SingleSelectMaster</a></td>
    </tr>
    <tr>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.table.Table">sap.ui.table.Table</a></td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.table.SelectionMode">Single</a></td>
    </tr>
    <tr>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.comp.smarttable.SmartTable">sap.ui.comp.smarttable.SmartTable</a></td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.table.SelectionMode">Single</a></td>
    </tr>
    <tr>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.table.AnalyticalTable">sap.ui.table.AnalyticalTable</a></td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.table.SelectionMode">Single</a></td>
    </tr>
    <tr>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.table.TreeTable">sap.ui.table.TreeTable</a></td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.table.SelectionMode">Single</a></td>
    </tr>
  </tbody>
</table>

[TABLE_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.Table
[BUTTON_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.Button

!!! example

    Let us consider an `EntitySet` named **Products**, which is bound to an [sap.m.Table][TABLE_URL] on the XML view. Our objective is to add an [sap.m.Button][BUTTON_URL] to the header toolbar of the table. When the user selects a row from the table and presses the **Display Product Details** button, a dialog will open so the user can view the details of the selected line.

![Read Constructor Sample](./images/read_entry/read_constructor_1.png)

!!! tip "Tip for TypeScript"

    The **EntryReadCL&lt;EntityT, EntityKeysT&gt;** class is a generic class and can be initialized with 2 types.

    - The `EntityT` type contains **all** properties of the `EntitySet` that is specified on the class constructor.
    - The `EntityKeysT` type contains the **key** properties of the `EntitySet` that is specified on the class constructor.

    The `EntityKeysT` type is utilized as one of the types of the `initializer` parameter in the class [constructor](#constructor).

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 14-17 22-25 30-33"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryReadCL from "ui5/antares/entry/v2/EntryReadCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onDisplayCategoryDetails() {
        // Initialize without a type and with the table id
        const entry = new EntryReadCL(this, {
          entityPath: "Categories",
          initializer: "tblCategories" // table id       
        }); 
      }

      public async onDisplayProductDetails() {
        // Initialize with a type and the table id
        const entry = new EntryReadCL<IProducts, IProductKeys>(this, {
          entityPath: "Products",
          initializer: "tblProducts" // table id       
        }); 
      }

      public async onDisplayCustomerDetails() {
        // Initialize with a model name and the table id
        const entry = new EntryReadCL(this, {
          entityPath: "Customers",
          initializer: "tblCustomers" // table id      
        }, "myODataModelName"); 
      }
    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string;
      supplierID: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18-21 26-29"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryReadCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryReadCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onDisplayProductDetails: async function () {
              // Initialize with the table id
              const entry = new EntryReadCL(this, {
                entityPath: "Products",
                initializer: "tblProducts" // table id                
              }); 
            },

            onDisplayCategoryDetails: async function () {
              // Initialize with a model name
              const entry = new EntryReadCL(this, {
                entityPath: "Categories",
                initializer: "tblCategories" // table id                 
              }, "myODataModelName");
            }
          });

        });
    ```

### Constructor with a Context Binding

An alternative approach to constructing an object from the [Entry Read](#entry-read) class is to utilise the [context](https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Context) of the entity that will be displayed by the end user.

!!! example

    Let us consider an `EntitySet` named **Products**, which is bound to an [sap.m.Table][TABLE_URL] on the XML view. Our objective is to add an [sap.m.Button][BUTTON_URL] to the header toolbar of the table. When the user selects a row from the table and presses the **Display Product Details** button, we will retrieve the context of the selected row and use it to construct an object from the [Entry Read](#entry-read) class.

![Read Constructor Sample](./images/read_entry/read_constructor_1.png)

!!! tip "Tip for TypeScript"

    The **EntryReadCL&lt;EntityT, EntityKeysT&gt;** class is a generic class and can be initialized with 2 types.

    - The `EntityT` type contains **all** properties of the `EntitySet` that is specified on the class constructor.
    - The `EntityKeysT` type contains the **key** properties of the `EntitySet` that is specified on the class constructor.

    The `EntityKeysT` type is utilized as one of the types of the `initializer` parameter in the class [constructor](#constructor).

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 16 18-21 24 27-30 35 37-40 43 46-49 54 56-59 62 65-68"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryReadCL from "ui5/antares/entry/v2/EntryReadCL"; // Import the class
    import MessageBox from "sap/m/MessageBox";
    import Table from "sap/m/Table";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onDisplayCategoryDetails() {
        // Get the selected item and warn the end user if no row was selected
        const selectedItem = (this.getView().byId("tblCategories") as Table).getSelectedItem();

        if (!selectedItem) {
          MessageBox.error("Please select a row from the table");
          return;
        }

        // Get the selected context
        const selectedContext = selectedItem.getBindingContext();

        // Initialize without a type and use the binding context
        const entry = new EntryReadCL(this, {
          entityPath: "Categories",
          initializer: selectedContext // binding context
        }); 
      }

      public async onDisplayProductDetails() {
        // Get the selected item and warn the end user if no row was selected
        const selectedItem = (this.getView().byId("tblProducts") as Table).getSelectedItem();

        if (!selectedItem) {
          MessageBox.error("Please select a row from the table");
          return;
        }

        // Get the selected context
        const selectedContext = selectedItem.getBindingContext();

        // Initialize with a type and use the binding context
        const entry = new EntryReadCL<IProducts, IProductKeys>(this, {
          entityPath: "Products",
          initializer: selectedContext // binding context
        }); 
      }

      public async onDisplayCustomerDetails() {
        // Get the selected item and warn the end user if no row was selected
        const selectedItem = (this.getView().byId("tblCustomers") as Table).getSelectedItem();

        if (!selectedItem) {
          MessageBox.error("Please select a row from the table");
          return;
        }

        // Get the selected context
        const selectedContext = selectedItem.getBindingContext();

        // Initialize with a model name and use the binding context
        const entry = new EntryReadCL(this, {
          entityPath: "Customers",
          initializer: selectedContext // binding context   
        }, "myODataModelName"); 
      }
    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string;
      supplierID: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 19 21-24 27 30-33 38 40-43 46 49-52"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryReadCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryReadCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onDisplayProductDetails: async function () {
              // Get the selected item and warn the end user if no row was selected
              const selectedItem = this.getView().byId("tblProducts").getSelectedItem();

              if (!selectedItem) {
                MessageBox.error("Please select a row from the table");
                return;
              }

              // Get the selected context
              const selectedContext = selectedItem.getBindingContext();

              // Initialize with the binding context
              const entry = new EntryReadCL(this, {
                entityPath: "Products",
                initializer: selectedContext // binding context     
              }); 
            },

            onDisplayCategoryDetails: async function () {
              // Get the selected item and warn the end user if no row was selected
              const selectedItem = this.getView().byId("tblCategories").getSelectedItem();

              if (!selectedItem) {
                MessageBox.error("Please select a row from the table");
                return;
              }

              // Get the selected context
              const selectedContext = selectedItem.getBindingContext();

              // Initialize with the binding context
              const entry = new EntryReadCL(this, {
                entityPath: "Categories",
                initializer: selectedContext // binding context               
              }, "myODataModelName");
            }
          });

        });
    ```

### Constructor with Entity Keys

The final method for constructing an object from the [Entry Read](#entry-read) class is to utilize the **key** values of the entity that will be displayed by the end user.

!!! example

    For the purposes of this example, let us consider an `EntitySet` named **Products** with a single **key** property named `ID`, whose type is `Edm.Guid`. We would like to allow the end user to display a specific entity with the key value: **ID = "b2f0013e-418f-42aa-9a24-3770fe17ce18"**.

!!! tip

    Please note that if the `EntitySet` is bound to a table, you can retrieve the values of the **key** properties of the selected row using the **getBindingContext().getObject()** method.

!!! info

    The EntryReadCL class creates a binding context with the values of the specified **key** properties using the `initializer` parameter in the class [constructor](#constructor) and subsequently binds the created context to the dialog.

!!! tip "Tip for TypeScript"

    The **EntryReadCL&lt;EntityT, EntityKeysT&gt;** class is a generic class and can be initialized with 2 types.

    - The `EntityT` type contains **all** properties of the `EntitySet` that is specified on the class constructor.
    - The `EntityKeysT` type contains the **key** properties of the `EntitySet` that is specified on the class constructor.

    The `EntityKeysT` type is utilized as one of the types of the `initializer` parameter in the class [constructor](#constructor).

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 14-16 19-22 27-29 32-35 40-42 45-48"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryReadCL from "ui5/antares/entry/v2/EntryReadCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onDisplayCategoryDetails() {
        // Prepare the key values of a specific entity
        const keyValues = {
          ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18"
        };

        // Initialize without a type and use the key values as the initializer
        const entry = new EntryReadCL(this, {
          entityPath: "Categories",
          initializer: keyValues // key values of the entity
        });
      }

      public async onDisplayProductDetails() {
        // Prepare the key values of a specific entity
        const keyValues = {
          ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18"
        };

        // Initialize with a type and use the key values as the initializer
        const entry = new EntryReadCL<IProducts, IProductKeys>(this, {
          entityPath: "Products",
          initializer: keyValues // key values of the entity
        });
      }

      public async onDisplayCustomerDetails() {
        // Prepare the key values of a specific entity
        const keyValues = {
          ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18"
        };

        // Initialize with a model name and use the key values as the initializer
        const entry = new EntryReadCL(this, {
          entityPath: "Customers",
          initializer: keyValues // key values of the entity
        }, "myODataModelName"); 
      }
    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string;
      supplierID: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18-20 23-26 31-33 36-39"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryReadCL"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryReadCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onDisplayProductDetails: async function () {
              // Prepare the key values of a specific entity
              const keyValues = {
                ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18"
              };

              // Initialize with the entity set name and use the key values as the initializer
              const entry = new EntryReadCL(this, {
                entityPath: "Products",
                initializer: keyValues // key values of the entity
              });  
            },

            onDisplayCategoryDetails: async function () {
              // Prepare the key values of a specific entity
              const keyValues = {
                ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18"
              };

              // Initialize with the entity set name and use the key values as the initializer
              const entry = new EntryReadCL(this, {
                entityPath: "Categories",
                initializer: keyValues // key values of the entity        
              }, "myODataModelName");          
            }
          });

        });
    ```

## Select Row Message

If the object from the [Entry Read](#entry-read) class is constructed using the [Constructor with a Table ID](#constructor-with-a-table-id) approach, a default error message is displayed in an [sap.m.MessageBox.error][MESSAGEBOX_URL] to the end user when the user has not yet selected a row from the table.

To change the default message, the **setSelectRowMessage()** method can be utilized.

=== "Setter (setSelectRowMessage)"

    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Type</th>
          <th>Mandatory</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>message</td>
          <td><code>string</code></td>
          <td>Yes</td>
          <td>The message that is displayed when the end user has not selected a row from the table</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getSelectRowMessage)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>string</code></td>
          <td>Returns the value that was set using <strong>setSelectRowMessage()</strong> method. Default value is <strong>Please select a row from the table.</strong></td>
        </tr>
      </tbody>
    </table>

## Read Entry

[META_MODEL_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.model.odata.ODataMetaModel

The **readEntry()** method establishes a connection between the context, determined by the `initializer` parameter in the [class constructor](#constructor), and the dialog that is either automatically generated or loaded from the fragment placed in the application files. Once the context is linked, the generated or loaded dialog is opened.

The **readEntry()** method utilizes the [ODataMetaModel][META_MODEL_URL] to determine the `EntityType` of the `EntitySet` designated through the [constructor](#constructor). It then generates the form with the properties in the same order as the OData metadata, in accordance with the `EntityType`.

!!! note

    Please note that the labels are generated assuming that the naming convention of the `EntityType` is **camelCase**. For further details, please see the [Label Generation](./entry_create.md/#label-generation) section.

!!! danger "Attention"

    It is not possible to modify any of the properties of an `EntitySet` on the auto-generated dialog. This behaviour cannot be altered.

!!! warning

    Please be advised that the **readEntry()** method must be called after any configurations have been made through the public methods of the [Entry Read](#entry-read) class. Any configurations (form title, end button text, etc.) made after the **readEntry()** method will not be reflected. As a best practice, the **readEntry()** method should be called at the end of your code block.

!!! note

    The key properties with Edm.Guid type are not visible by default on the generated form. However, this behavior can be modified using the [setDisplayGuidProperties()](./entry_create.md/#properties-with-edmguid-type) method.

!!! danger "Attention"

    Please be advised that the random UUID generation for properties with the `Edm.Guid` type is not available in the [Entry Read](#entry-read) class.

### Method Parameters

<table>
  <thead>
    <tr>
      <th>Returns</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>Promise&lt;void&gt;</code></td>
      <td>Once the promise has been fulfilled, the bound entry can be retrieved using the <strong>getEntryContext()</strong> method with the object instantiated from the <a href="#constructor">EntryReadCL</a> class.</td>
    </tr>
  </tbody>
</table>

!!! info

	The **readEntry()** method uses the default configurations when creating the dialog. However, these configurations can be modified using the public setter methods.

### Default Values

<table>
    <thead>
        <tr>
            <th>Term</th>
            <th>Default Value</th>
            <th>Description</th>
            <th>Setter</th>
            <th>Getter</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Naming Strategy</td>
            <td><a href="../entry_create/#namingstrategies-enum">NamingStrategies.CAMEL_CASE</a></td>
            <td>The default naming strategy is <strong>CAMEL_CASE</strong></td>
            <td><a href="../entry_create/#naming-strategy">setNamingStrategy()</a></td>
            <td><a href="../entry_create/#naming-strategy">getNamingStrategy()</a></td>
        </tr>
        <tr>
            <td>Resource Bundle Prefix</td>
            <td><code>antares</code></td>
            <td>The default resource bundle prefix is <strong>antares</strong></td>
            <td><a href="../entry_create/#resource-bundle-prefix">setResourceBundlePrefix()</a></td>
            <td><a href="../entry_create/#resource-bundle-prefix">getResourceBundlePrefix()</a></td>
        </tr>
        <tr>
            <td>Use Metadata Labels</td>
            <td><code>false</code></td>
            <td>The labels are not derived from the metadata, but rather generated.</td>
            <td><a href="../entry_create/#use-metadata-labels">setUseMetadataLabels()</a></td>
            <td><a href="../entry_create/#use-metadata-labels">getUseMetadataLabels()</a></td>
        </tr>
        <tr>
            <td>Form Type</td>
            <td><a href="../entry_create/#formtypes-enum">FormTypes.SMART</a></td>
            <td>The SmartForm with SmartFields is generated as the default option.</td>
            <td><a href="../entry_create/#form-type">setFormType()</a></td>
            <td><a href="../entry_create/#form-type">getFormType()</a></td>
        </tr>
        <tr>
            <td>Form Title</td>
            <td><code>Read + ${entityPath}</code></td>
            <td>The <code>entityPath</code> parameter of the <a href="#constructor">constructor</a> is used</td>
            <td><a href="../entry_create/#form-title">setFormTitle()</a></td>
            <td><a href="../entry_create/#form-title">getFormTitle()</a></td>
        </tr>
        <tr>
            <td>End Button Text</td>
            <td><code>Close</code></td>
            <td>The default end button text is <strong>Close</strong></td>
            <td><a href="../entry_create/#end-button-text">setEndButtonText()</a></td>
            <td><a href="../entry_create/#end-button-text">getEndButtonText()</a></td>
        </tr>
        <tr>
            <td>End Button Type</td>
            <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType">ButtonType.Negative</a></td>
            <td>The default button type is <strong>Negative</strong></td>
            <td><a href="../entry_create/#end-button-type">setEndButtonType()</a></td>
            <td><a href="../entry_create/#end-button-type">getEndButtonType()</a></td>
        </tr>
    </tbody>
</table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 22 42 64"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryReadCL from "ui5/antares/entry/v2/EntryReadCL"; // Import the class
    import Table from "sap/m/Table";
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onDisplayProductDetails() {
        // Initialize without a type and with a table id
        const entry = new EntryReadCL(this, {
          entityPath: "Products",
          initializer: "tblProducts"
        });

        // Call 
        entry.readEntry(false); 
      }

      public async onDisplayCategoryDetails() {
        const selectedItem = (this.getView().byId("tblCategories") as Table).getSelectedItem();

        if (!selectedItem) {
          MessageBox.error("Please select a row from the table");
          return;
        }

        const selectedContext = selectedItem.getBindingContext();

        // Initialize with a type and a binding context
        const entry = new EntryReadCL<ICategory>(this, {
          entityPath: "Categories",
          initializer: selectedContext
        }); 

        // Call
        entry.readEntry();
      }

      public async onDisplayCustomerDetails () {
        const selectedItem = (this.getView().byId("tblCustomers") as Table).getSelectedItem();

        if (!selectedItem) {
          MessageBox.error("Please select a row from the table");
          return;
        };

        const customerKeys = {
          ID: selectedItem.getBindingContext().getObject().ID
        };

        // Initialize without a type and with the key values
        const entry = new EntryReadCL(this, {
          entityPath: "Customers",
          initializer: customerKeys
        });

        // Call
        entry.readEntry();
      }
    }

    interface ICategory {
      ID: string;
      name?: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 25 45 67"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryReadCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryReadCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onDisplayProductDetails: async function () {
              // Initialize with a table id
              const entry = new EntryReadCL(this, {
                entityPath: "Products",
                initializer: "tblProducts"
              }); 

              // Call
              entry.readEntry();
            },

            onDisplayCategoryDetails: async function () {
              const selectedItem = this.getView().byId("tblCategories").getSelectedItem();

              if (!selectedItem) {
                MessageBox.error("Please select a row from the table");
                return;
              }

              const selectedContext = selectedItem.getBindingContext();

              // Initialize with a binding context
              const entry = new EntryReadCL(this, {
                entityPath: "Categories",
                initializer: selectedContext
              }); 

              // Call
              entry.readEntry();
            },

            onDisplayCustomerDetails: async function () {
              const selectedItem = this.getView().byId("tblCustomers").getSelectedItem();

              if (!selectedItem) {
                MessageBox.error("Please select a row from the table");
                return;
              }

              const customerKeys = {
                ID: selectedItem.getBindingContext().getObject().ID
              };

              // Initialize with the key values
              const entry = new EntryReadCL(this, {
                entityPath: "Customers",
                initializer: customerKeys
              }); 

              // Call
              entry.readEntry();          
            }
          });

        });
    ```

The generated form with default values will be similar in appearance to the following example. However, it should be noted that the exact appearance may vary depending on the configurations and the `EntityType` properties of the `EntitySet`.

![Read Entry](./images/read_entry/read_entry.png)

## Available Features

The [EntryReadCL](#entry-read) class is derived from the same abstract class as the [EntryCreateCL][ENTRY_CREATE_URL] class and contains the same methods. However, some of these functions are not applicable to the [EntryReadCL](#entry-read) class. 

!!! warning 

    Please note that the default values for the available functions may differ.

The features listed below are identical to those available in [EntryCreateCL][ENTRY_CREATE_URL]. Methods can be accessed through the object constructed from the [EntryReadCL](#entry-read) class.

!!! tip

    To access the documentation for a particular feature, please click on the name of the feature.

<table>
  <thead>
    <tr>
      <th>Feature</th>
      <th>Availability</th>
      <th>Default Value</th>
      <th>Remarks</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="../create_entry/#manual-submit">Manual Submit</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>  
    <tr>
      <td><a href="../create_entry/#disable-auto-dialog-close">Disable Auto Dialog Close</a></td>
      <td align="center">&#x2717;</td>
      <td>false</td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#label-generation">Label Generation</a></td>
      <td align="center">&#x2714;</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#resource-bundle-prefix">Resource Bundle Prefix</a></td>
      <td align="center">&#x2714;</td>
      <td>antares</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#naming-strategy">Naming Strategy</a></td>
      <td align="center">&#x2714;</td>
      <td>CAMEL_CASE</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#form-type">Form Type</a></td>
      <td align="center">&#x2714;</td>
      <td>SMART</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#form-title">Form Title</a></td>
      <td align="center">&#x2714;</td>
      <td>Read <code>${entityPath}</code></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#form-grouping">Form Grouping</a></td>
      <td align="center">&#x2714;</td>
      <td>[]</td>
      <td></td>
    </tr> 
    <tr>
      <td><a href="../create_entry/#custom-data">Custom Data</a></td>
      <td align="center">&#x2714;</td>
      <td>[]</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#text-in-edit-mode-source">Text In Edit Mode Source</a></td>
      <td align="center">&#x2717;</td>
      <td>[]</td>
      <td></td>
    </tr>       
    <tr>
      <td><a href="../create_entry/#begin-button-text">Begin Button Text</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#begin-button-type">Begin Button Type</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#end-button-text">End Button Text</a></td>
      <td align="center">&#x2714;</td>
      <td>Close</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="../create_entry/#end-button-type">End Button Type</a></td>
      <td align="center">&#x2714;</td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType">ButtonType.Negative</a></td>
      <td></td>
    </tr>   
    <tr>
      <td><a href="../create_entry/#properties-with-edmguid-type">Properties with Edm.Guid Type</a></td>
      <td align="center">&#x2714;</td>
      <td></td>
      <td>The random UUID generation is not available. You can only modify the visibilities of the properties with <code>Edm.Guid</code> type</td>
    </tr>
    <tr>
      <td><a href="../create_entry/#form-property-order">Form Property Order</a></td>
      <td align="center">&#x2714;</td>
      <td>[]</td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#excluded-properties">Excluded Properties</a></td>
      <td align="center">&#x2714;</td>
      <td>[]</td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#mandatory-properties">Mandatory Properties</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#readonly-properties">Readonly Properties</a></td>
      <td align="center">&#x2717;</td>
      <td>[all properties]</td>
      <td>By default, all the properties are readonly and cannot be changed</td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#attach-submit-completed">Attach Submit Completed</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#attach-submit-failed">Attach Submit Failed</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#response-class">Response Class</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../value-help">Value Help</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#validation-logic">Validation Logic</a></td>
      <td align="center">&#x2717;</td>
      <td></td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#object-page">Object Page</a></td>
      <td align="center">&#x2714;</td>
      <td></td>
      <td></td>
    </tr>      
    <tr>
      <td><a href="../create_entry/#custom-control">Custom Control</a></td>
      <td align="center">&#x2714;</td>
      <td></td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#custom-content">Custom Content</a></td>
      <td align="center">&#x2714;</td>
      <td></td>
      <td></td>
    </tr>    
    <tr>
      <td><a href="../create_entry/#custom-fragment">Custom Fragment</a></td>
      <td align="center">&#x2714;</td>
      <td></td>
      <td></td>
    </tr>    
  </tbody>
</table>