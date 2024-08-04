# Entry Create

The EntryCreateCL class is responsible for managing the CREATE (POST) operation through the OData V2 model. This class eliminates the need for developers to concern themselves with fragments, user input validations, and value help creation when working on custom SAPUI5 applications or Fiori Elements extensions. The following section outlines the key features of the Entry Create class.

## Features

[DIALOG_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.Dialog
[SIMPLEFORM_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.layout.form.SimpleForm
[SMARTFORM_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.comp.smartform.SmartForm
[OBJECT_PAGE_URL]: https://sapui5.hana.ondemand.com/#/api/sap.uxap.ObjectPageLayout

- [sap.m.Dialog][DIALOG_URL] generation with a [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL], or custom content
- [sap.uxap.ObjectPageLayout][OBJECT_PAGE_URL] generation with a [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL], or custom content
- User input validation via the [ValidationLogicCL](#validation-logic) class
- Value Help Dialog generation via the [ValueHelpCL](#value-help) class
- Property sorting, readonly properties, UUID generation for the properties with `Edm.Guid` type
- Label generation for the [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL] elements
- **createEntry()**, **submitChanges()**, and **resetChanges()** handling based on the user interaction
- Call a fragment and bind the context in case you do not want to use the auto-generated dialog

## Use Case

[SMARTFIELD_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.comp.smartfield.SmartField

Let's assume you have an EntitySet named **Products** and wish to provide your end-user with the capability to create a new entity via a pop-up screen using the OData V2 service in your custom SAPUI5 application. The following steps are required to achieve this functionality.

1) It is necessary to create a **.fragment.xml** file that contains a dialog with form content (Simple, Smart, etc.) and to call it from the controller or to generate the dialog directly on the controller.

2) If you do not use the [sap.ui.comp.smartfield.SmartField][SMARTFIELD_URL] component with the OData Annotations, you will need to write a significant amount of Value Help code.

3) It is essential to validate user input, such as checking mandatory fields and ensuring that the values entered align with your business logic.

4) It is necessary to create a transient entity (createEntry) and either submit it or reset it based on the user interaction.

The [EntryCreateCL](#entry-create) class is responsible for executing all of the steps mentioned above.

## Constructor

[CONTROLLER_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.core.mvc.Controller

In order to utilise the functionality of EntryCreateCL, it is necessary to initialise the object.

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
            <td>entityPath</td>
            <td><code>string</code></td>
            <td>Yes</td>
            <td></td>
            <td>The name of the <strong>EntitySet</strong>. It can start with a <strong>"/"</strong> (slash)</td>
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

!!! tip "Tip for TypeScript"

    The **EntryCreateCL&lt;EntityT&gt;** class is a generic class that can be initialized with a type that contains the properties of the EntitySet used as a parameter on the class constructor. The `EntityT` type is used as the `data?` parameter type for the **createNewEntry(data?: EntityT)** method.    

    Additionally, it is utilized as the return type of the **getResponse(): EntityT** method of the `ResponseCL` class, whose object is passed as a parameter into the function attached by the **attachSubmitCompleted(submitCompleted: (response: ResponseCL&lt;EntityT&gt;) => void, listener?: object)** method.


=== "TypeScript"

    ```ts
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
    * @namespace your.apps.namespace
    */
    export default class YourController extends Controller {
        public onInit() {

        }

        public async onCreateProduct() {
            // Initialize without a type
            const entry = new EntryCreateCL(this, "Products"); 
        }

        public async onCreateCategory() {
            // Initialize with a type
            const entry = new EntryCreateCL<ICategory>(this, "Categories"); 
        }

        public async onCreateCustomer() {
            // Initialize with a model name
            const entry = new EntryCreateCL(this, "Customers", "myODataModelName"); 
        }
    }

    interface ICategory {
        ID: string;
        name?: string;
    }
    ```

=== "JavaScript"

    ```js
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryCreateCL" // Import the class
    ], 
        /**
        * @param {typeof sap.ui.core.mvc.Controller} Controller
        */
        function (Controller, EntryCreateCL) {
            "use strict";

            return Controller.extend("your.apps.namespace.YourController", {
                onInit: function () {

                },

                onCreateProduct: async function () {
                    // Initialize
                    const entry = new EntryCreateCL(this, "Products"); 
                },

                onCreateCategory: async function () {
                    // Initialize with a model name
                    const entry = new EntryCreateCL(this, "Categories", "myODataModelName");
                }
            });

        });
    ```

## Create New Entry

[CREATE_ENTRY_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.model.odata.v2.ODataModel%23methods/createEntry
[META_MODEL_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.model.odata.ODataMetaModel

**The createNewEntry(data?: EntityT)** method creates an entry for the `EntitySet` specified through the [class constructor](#constructor) and binds it to the automatically generated or loaded dialog, which is located in the application files. The [createEntry()][CREATE_ENTRY_URL] method from the SAPUI5 library is utilized to create an entry, and the dialog is then opened after the entry is created.

The **createNewEntry()** method utilizes the [ODataMetaModel][META_MODEL_URL] to determine the `EntityType` of the `EntitySet` designated through the [constructor](#constructor). It then generates the form with the properties in the same order as the OData metadata, in accordance with the `EntityType`.

!!! note

    Please note that all **key** properties are marked as mandatory/required, and the labels are generated assuming that the naming convention of the `EntityType` is **camelCase**. For further details, please see the [Label Generation](#label-generation) section.

!!! warning

    Please be advised that the **createNewEntry()** method must be called after any configurations have been made through the public methods of the [Entry Create](#entry-create) class. Any configurations (form title, mandatory properties, etc.) made after the **createNewEntry()** method will not be reflected. As a best practice, the **createNewEntry()** method should be called at the end of your code block.

!!! note

    The default setting for **key** properties with the `Edm.Guid` type is to generate a random UUID value, which is not visible on the generated form. However, this behavior can be modified using the [setGenerateRandomGuid()](#properties-with-edmguid-type) and [setDisplayGuidProperties()](#properties-with-edmguid-type) methods.

### Method Parameters

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
            <td>data?</td>
            <td><code>EntityT</code> or <code>object</code></td>
            <td>No</td>
            <td><code>undefined</code></td>
            <td>The initial values of the entry</td>
        </tr>
    </tbody>
</table>

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
            <td>Once the promise has been fulfilled, the newly created entry can be retrieved using the <strong>getEntryContext()</strong> method with the object instantiated from the <a href="#constructor">EntryCreateCL</a> class.</td>
        </tr>
    </tbody>
</table>

!!! info

    The **createNewEntry()** method utilizes the default configurations when creating the dialog. However, these configurations can be modified through the use of the public setter methods.

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
            <td><a href="#namingstrategies-enum">NamingStrategies.CAMEL_CASE</a></td>
            <td>The default naming strategy is <strong>CAMEL_CASE</strong></td>
            <td><a href="#naming-strategy">setNamingStrategy()</a></td>
            <td><a href="#naming-strategy">getNamingStrategy()</a></td>
        </tr>
        <tr>
            <td>Resource Bundle Prefix</td>
            <td><code>antares</code></td>
            <td>The default resource bundle prefix is <strong>antares</strong></td>
            <td><a href="#resource-bundle-prefix">setResourceBundlePrefix()</a></td>
            <td><a href="#resource-bundle-prefix">getResourceBundlePrefix()</a></td>
        </tr>
        <tr>
            <td>Use Metadata Labels</td>
            <td><code>false</code></td>
            <td>The labels are not derived from the metadata, but rather generated.</td>
            <td><a href="#use-metadata-labels">setUseMetadataLabels()</a></td>
            <td><a href="#use-metadata-labels">getUseMetadataLabels()</a></td>
        </tr>
        <tr>
            <td>Form Type</td>
            <td><a href="#formtypes-enum">FormTypes.SMART</a></td>
            <td>The SmartForm with SmartFields is generated as the default option.</td>
            <td><a href="#form-type">setFormType()</a></td>
            <td><a href="#form-type">getFormType()</a></td>
        </tr>
        <tr>
            <td>Form Title</td>
            <td><code>Create New + ${entityPath}</code></td>
            <td>The <code>entityPath</code> parameter of the <a href="#constructor">constructor</a> is used</td>
            <td><a href="#form-title">setFormTitle()</a></td>
            <td><a href="#form-title">getFormTitle()</a></td>
        </tr>
        <tr>
            <td>Begin Button Text</td>
            <td><code>Create</code></td>
            <td>The default begin button text is <strong>Create</strong></td>
            <td><a href="#begin-button-text">setBeginButtonText()</a></td>
            <td><a href="#begin-button-text">getBeginButtonText()</a></td>
        </tr>
        <tr>
            <td>Begin Button Type</td>
            <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType">ButtonType.Success</a></td>
            <td>The default button type is <strong>Success</strong></td>
            <td><a href="#begin-button-type">setBeginButtonType()</a></td>
            <td><a href="#begin-button-type">getBeginButtonType()</a></td>
        </tr>
        <tr>
            <td>End Button Text</td>
            <td><code>Close</code></td>
            <td>The default end button text is <strong>Close</strong></td>
            <td><a href="#end-button-text">setEndButtonText()</a></td>
            <td><a href="#end-button-text">getEndButtonText()</a></td>
        </tr>
        <tr>
            <td>End Button Type</td>
            <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType">ButtonType.Negative</a></td>
            <td>The default button type is <strong>Negative</strong></td>
            <td><a href="#end-button-type">setEndButtonType()</a></td>
            <td><a href="#end-button-type">getEndButtonType()</a></td>
        </tr>
        <tr>
            <td>Mandatory Error Message</td>
            <td><code>Please fill in all required fields.</code></td>
            <td>The displayed message when the mandatory check is unsuccessful</td>
            <td><a href="#mandatory-error-message">setMandatoryErrorMessage()</a></td>
            <td><a href="#mandatory-error-message">getMandatoryErrorMessage()</a></td>
        </tr>
    </tbody>
</table>

=== "TypeScript"

    ```ts
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        // Initialize without a type
        const entry = new EntryCreateCL(this, "Products");

        // Call without the initial values
        entry.createNewEntry(); 
      }

      public async onCreateCategory() {
        // Initialize with a type
        const entry = new EntryCreateCL<ICategory>(this, "Categories"); 

        // Call with the initial values
        entry.createNewEntry({
          ID: "ELEC",
          name: "Electronics"
        });
      }
    }

    interface ICategory {
      ID: string;
      name?: string;
    }
    ```

=== "JavaScript"

    ```js
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryCreateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryCreateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // Initialize
              const entry = new EntryCreateCL(this, "Products"); 

              // Call without the initial values
              entry.createNewEntry();
            },

            onCreateCategory: async function () {
              // Initialize
              const entry = new EntryCreateCL(this, "Categories"); 

              // Call with the initial values
              entry.createNewEntry({
                ID: "ELEC",
                name: "Electronics"
              });
            }
          });

        });
    ```

The generated form with default values will be similar in appearance to the following example. However, it should be noted that the exact appearance may vary depending on the configurations and the `EntityType` properties of the `EntitySet`.

![Generated Form](./images/create_entry/create_new_entry_default.png)

## Manual Submit

[ENTRY_UPDATE_URL]: ./entry_update.md

As a default setting, any changes made by the end user on the auto-generated form will be automatically submitted by the [Entry Create](#entry-create) and [Entry Update][ENTRY_UPDATE_URL] classes upon pressing the **begin button**. There may be a necessity, however, to run some code prior to submitting the changes via the OData V2 model.

It is possible to register a function that will be called in replacement of the automatic submit mechanism when the end user presses the **begin button**.

!!! danger "Attention"

    - Please be advised that the manual submit feature is only available for the [Entry Create](#entry-create) and [Entry Update][ENTRY_UPDATE_URL] classes.
    - Please be advised that this feature cannot be used in conjunction with the [Object Page](#object-page) feature.

To register a function, the **registerManualSubmit()** method can be used. Upon pressing the begin button, the registered function will be called, and an object constructed from the [Entry Create](#constructor) or [Entry Update][ENTRY_UPDATE_URL] class will be passed as a parameter to the function.

Once you have run your own code in the registered function, please call the **submitManually()** method using the object passed as a parameter to the function in order to complete the process.

Furthermore, the auto-generated dialog ([sap.m.Dialog][DIALOG_URL]) can be obtained by calling the **getGeneratedDialog()** method with the object passed as a parameter to the function.

=== "Setter (registerManualSubmit)"

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
                <td>submitter</td>
                <td><code>(entry: EntryCreateCL&lt;EntityT&gt; | EntryUpdateCL&lt;EntityT&gt;) => void</code></td>
                <td>Yes</td>
                <td>The function that will be called when the user presses the begin button</td>
            </tr>
        </tbody>
    </table>

=== "Getter (getGeneratedDialog)"

    <table>
        <thead>
            <tr>
                <th>Returns</th>
                <th>Description</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.Dialog">sap.m.Dialog</a></td>
                <td>Returns the auto-generated dialog</td>
            </tr>
        </tbody>
    </table>

!!! example

    Let us consider an `EntitySet` named **Products**. Before submitting the changes through the OData V2 model, we would like to run some code.

=== "TypeScript"

    ```ts
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import EntryUpdateCL from "ui5/antares/entry/v2/EntryUpdateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public onCreateProduct() {
        // initialize
        const entry = new EntryCreateCL<IProducts>(this, "Products");

        // register the submitter function
        entry.registerManualSubmit(this.createProductManually, this);

        // call the dialog
        entry.createNewEntry();
      }

      // use the same generic type with the constructor in onCreateProduct method
      private async createProductManually(entry: EntryCreateCL<IProducts>) {
        // obtain the generated dialog
        const dialog = entry.getGeneratedDialog();

        dialog.getContent().forEach((content) => {
          // here you can access each element of the dialog
        });

        // run your own code (can also be async)

        // do not forget to complete the submit process
        entry.submitManually();
      }

      public async onUpdateProduct() {
        // Initialize with a type and use the table id as the initializer
        const entry = new EntryUpdateCL<IProducts, IProductKeys>(this, {
          entityPath: "Products",
          initializer: "tblProducts" // table id
        });

        // register the submitter function
        entry.registerManualSubmit(this.updateProductManually, this);

        // call the dialog
        entry.updateEntry();    
      }

      // use the same generic type with the constructor in onUpdateProduct method
      private async updateProductManually(entry: EntryUpdateCL<IProducts>) {
        // obtain the generated dialog
        const dialog = entry.getGeneratedDialog();

        dialog.getContent().forEach((content) => {
          // here you can access each element of the dialog
        });

        // run your own code (can also be async)

        // do not forget to complete the submit process
        entry.submitManually();    
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

    ```js
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryCreateCL", // Import the class
        "ui5/antares/entry/v2/EntryUpdateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryCreateCL, EntryUpdateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // initialize
              const entry = new EntryCreateCL(this, "Products");

              // register the submitter function
              entry.registerManualSubmit(this._createProductManually, this);

              // call the dialog
              entry.createNewEntry(); 
            },

            _createProductManually: async function (entry) {
              // obtain the generated dialog
              const dialog = entry.getGeneratedDialog();

              dialog.getContent().forEach((content) => {
                // here you can access each element of the dialog
              });

              // run your own code (can also be async)

              // do not forget to complete the submit process
              entry.submitManually();          
            },

            onUpdateProduct: async function () {
              // Initialize and use the table id as the initializer
              const entry = new EntryUpdateCL(this, {
                entityPath: "Products",
                initializer: "tblProducts" // table id
              });

              // register the submitter function
              entry.registerManualSubmit(this._updateProductManually, this);

              // call the dialog
              entry.updateEntry();      
            },

            _updateProductManually: async function (entry) {
              // obtain the generated dialog
              const dialog = entry.getGeneratedDialog();

              dialog.getContent().forEach((content) => {
                // here you can access each element of the dialog
              });

              // run your own code (can also be async)

              // do not forget to complete the submit process
              entry.submitManually();    
            }

          });

        });
    ```

## Disable Auto Dialog Close

The generated dialog is closed and destroyed after the submission is completed by default in the [Entry Create](#entry-create) and [Entry Update][ENTRY_UPDATE_URL] classes. However, this auto-close feature can be disabled if you still need to access the dialog content after the submission. This feature is particularly useful when there are custom contents that will be managed after the submission is completed.

!!! tip

    To close and destroy the dialog afterwards, the **closeAndDestroyEntryDialog()** method can be utilized through the object instantiated from the [Entry Create](#constructor) or [Entry Update][ENTRY_UPDATE_URL] class.

To disable the auto close feature, the **setDisableAutoClose()** method can be utilized.

=== "Setter (setDisableAutoClose)"

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
                <td>disable</td>
                <td><code>boolean</code></td>
                <td>Yes</td>
                <td>When this parameter is set to <strong>true</strong>, the auto close feature will be disabled</td>
            </tr>
        </tbody>
    </table>

=== "Getter (getDisableAutoClose)"

    <table>
        <thead>
            <tr>
                <th>Returns</th>
                <th>Description</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td><code>boolean</code></td>
                <td>Returns the value that was set using <strong>setDisableAutoClose()</strong> method. Default value is <strong>false</strong></td>
            </tr>
        </tbody>
    </table>

**Example**

=== "TypeScript"

    ```ts
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import ResponseCL from "ui5/antares/entry/v2/ResponseCL"; // Import the response class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      private productEntry: EntryCreateCL<IProducts>;

      public onInit() {

      }

      public onCreateProduct() {
        // initialize and set to the class property
        this.productEntry = new EntryCreateCL<IProducts>(this, "Products");

        // disable auto close
        this.productEntry.setDisableAutoClose(true);

        // attach submit completed
        this.productEntry.attachSubmitCompleted(this.onCreateCompleted, this);

        // call the dialog
        this.productEntry.createNewEntry();
      }

      private onCreateCompleted(response: ResponseCL<IProducts>) {
        // do your logic here 

        // do not forget to close and destroy
        this.productEntry.closeAndDestroyEntryDialog();
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

    ```js
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryCreateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryCreateCL) {
          "use strict";
    
          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {
            
            },
    
            onCreateProduct: async function () {
              // initialize and set to the class property
              this.productEntry = new EntryCreateCL<IProducts>(this, "Products");
    
              // disable auto close
              this.productEntry.setDisableAutoClose(true);
    
              // attach submit completed
              this.productEntry.attachSubmitCompleted(this._onCreateCompleted, this);
    
              // call the dialog
              this.productEntry.createNewEntry();
            },
    
            _onCreateCompleted: function (response) {
              // do your logic here 
    
              // do not forget to close and destroy
              this.productEntry.closeAndDestroyEntryDialog();
            }
    
          });
    
        });
    ```

## Label Generation

### Use Metadata Labels

## Value Help

## Validation Logic

## Properties with Edm.Guid Type

## Naming Strategy

### NamingStrategies Enum

## Form Type

### FormTypes Enum

## Resource Bundle Prefix

## Form Title

## Begin Button Text

## Begin Button Type

## End Button Text

## End Button Type

## Mandatory Properties

### Mandatory Error Message

## Object Page