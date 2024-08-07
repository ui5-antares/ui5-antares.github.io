# Entry Create

The EntryCreateCL class is responsible for managing the CREATE (POST) operation through the OData V2 model. This class eliminates the need for developers to concern themselves with fragments, user input validations, and value help creation when working on custom SAPUI5 applications or Fiori Elements extensions. The following section outlines the key features of the Entry Create class.

## Features

[DIALOG_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.Dialog
[SIMPLEFORM_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.layout.form.SimpleForm
[SMARTFORM_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.comp.smartform.SmartForm
[OBJECT_PAGE_URL]: https://sapui5.hana.ondemand.com/#/api/sap.uxap.ObjectPageLayout
[VALUEHELP_CLASS_URL]: ./value_help.md
[ENTRY_UPDATE_URL]: ./entry_update.md
[ENTRY_DELETE_URL]: ./entry_delete.md
[ENTRY_READ_URL]: ./entry_read.md

- [sap.m.Dialog][DIALOG_URL] generation with a [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL], or custom content
- [sap.uxap.ObjectPageLayout][OBJECT_PAGE_URL] generation with a [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL], or custom content
- User input validation via the [ValidationLogicCL](#validation-logic) class
- Value Help Dialog generation via the [ValueHelpCL][VALUEHELP_CLASS_URL] class
- Property sorting, readonly properties, UUID generation for the properties with `Edm.Guid` type
- Label generation for the [SmartForm][SMARTFORM_URL], [SimpleForm][SIMPLEFORM_URL] elements
- **createEntry()**, **submitChanges()**, and **resetChanges()** handling based on the user interaction
- Call a fragment and bind the context in case you do not want to use the auto-generated dialog

!!! info

	While the examples of the features on this page are based on the [Entry Create](#entry-create) class, the majority of the features are also available in the [Entry Update][ENTRY_UPDATE_URL], [Entry Delete][ENTRY_DELETE_URL], and [Entry Read][ENTRY_READ_URL] classes. 

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

    ``` ts linenums="1" hl_lines="2 14 19 24"
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

    ``` js linenums="1" hl_lines="3 18 23"
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

!!! example

    Let's assume you have an EntitySet named **Products** and wish to provide your end-user with the capability to create a new entity via a pop-up screen using the OData V2 service in your custom SAPUI5 application.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 17 25-28"
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

    ``` js linenums="1" hl_lines="3 21 29-32"
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

    ``` ts linenums="1" hl_lines="2 3 18 25-37 47 54-66"
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

    ``` js linenums="1" hl_lines="3 4 22 28-40 50 56-68"
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

    ``` ts linenums="1" hl_lines="2 3 20 23 29-34"
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

    ``` js linenums="1" hl_lines="3 21 24 30-35"
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

The [Entry Create](#entry-create) class is designed to generate labels for form elements within the auto-generated SmartForm/SimpleForm, as the default setting. The following steps will be followed during the creation of the labels.

!!! note
    
    In the event that the metadata labels are not utilized when creating the labels, the Resource Model will take precedence.

### Resource Model (i18n)

[RESOURCE_MODEL_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.model.resource.ResourceModel

In the event that the application in question has a [Resource Model][RESOURCE_MODEL_URL] named `i18n` within its **manifest.json** file, the [Entry Create](#entry-create) class will search for the texts associated with each property of the EntityType, operating under the assumption that the **key** of the i18n text is written in the format outlined below.

!!! info

    **Default format of the i18n keys:** antares + [entityPath](#constructor) + propertyName

In this context, `antares` is the default prefix, which can be modified using the [setResourceBundlePrefix()](#resource-bundle-prefix) method. The `entityPath` is derived from the [class constructor](#constructor), while the `propertyName` represents the technical name of an `EntityType` property in the OData V2 metadata.

```json title="manifest.json"
{
  "_version": "1.59.0",
  "sap.app": {
    ...
    "i18n": "path/to/i18n.properties"
  },
  ...
  "sap.ui5": {
    ...
    "models": {
      "i18n": {
          "type": "sap.ui.model.resource.ResourceModel",
          "settings": {
              "bundleName": "your.apps.namespace.i18n.i18n"
          }
      }      
    }
  }
}
```

!!! example

    If you have an `EntitySet` named **Products** with the properties shown in the metadata below, your application's `i18n.properties` file will be checked for the following keys.

```properties title="i18n.properties"
antaresProductsID=Label of the ID property
antaresProductsname=Label of the name property
antaresProductsdescription=Label of the description property
...
```

```xml title="$metadata.xml"
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="1.0" xmlns:edmx="http://schemas.microsoft.com/ado/2007/06/edmx" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata">
    <edmx:DataServices m:DataServiceVersion="2.0">
        <Schema Namespace="OnlineShopping" xmlns="http://schemas.microsoft.com/ado/2008/09/edm">
            <EntityContainer Name="EntityContainer" m:IsDefaultEntityContainer="true">
                <EntitySet Name="Products" EntityType="OnlineShopping.Product"/>
            </EntityContainer>
            <EntityType Name="Product">
                <Key>
                    <PropertyRef Name="ID"/>
                </Key>
                <Property Name="ID" Type="Edm.Guid" Nullable="false"/>
                <Property Name="name" Type="Edm.String" MaxLength="50"/>
                <Property Name="description" Type="Edm.String" MaxLength="255"/>
                <Property Name="brand" Type="Edm.String" MaxLength="50"/>
                <Property Name="price" Type="Edm.Decimal" Precision="13" Scale="2" Nullable="false"/>
                <Property Name="currency" Type="Edm.String" MaxLength="5" Nullable="false"/>
                <Property Name="quantityInStock" Type="Edm.Int32"/>
                <Property Name="categoryID" Type="Edm.Guid" Nullable="false"/>
                <Property Name="supplierID" Type="Edm.Guid" Nullable="false"/>
            </EntityType>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```

!!! info

    In the event that the [Resource Model](#resource-model-i18n) is unavailable or the text is not present in the `i18n.properties` file, the [Label Generation From The Technical Names](#label-generation-from-the-technical-names) process will be initiated.

### Label Generation From The Technical Names

In the event that a label cannot be generated from the [Resource Model](#resource-model-i18n), the [Entry Create](#entry-create) class attempts to parse the technical property names of the `EntityType` into **human-readable** words.

The default assumption is that the naming convention for `EntityType` properties is **camelCase**. However, if a different naming convention was used when creating these properties, [setNamingStrategy()](#naming-strategy) can be used to change the default naming convention.

!!! example

    The following examples illustrate the breakdown of property names into **human-readable** words in various naming strategies.

=== "camelCase"

    <table>
      <thead>
        <tr>
          <th>Technical Name</th>
          <th>Generated Label</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>productID</td>
          <td>Product ID</td>
        </tr>
        <tr>
          <td>firstName</td>
          <td>First Name</td>
        </tr>
        <tr>
          <td>lastName</td>
          <td>Last Name</td>
        </tr>
      </tbody>
    </table>

=== "PascalCase"

    **Note:** Uid, Id and Url words are accepted as special words and converted to upper case after splitting.

    <table>
      <thead>
        <tr>
          <th>Technical Name</th>
          <th>Generated Label</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>ProductId</td>
          <td>Product ID</td>
        </tr>
        <tr>
          <td>FirstName</td>
          <td>First Name</td>
        </tr>
        <tr>
          <td>LastName</td>
          <td>Last Name</td>
        </tr>
      </tbody>
    </table>

=== "kebab-case"

    <table>
      <thead>
        <tr>
          <th>Technical Name</th>
          <th>Generated Label</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>product-id</td>
          <td>Product Id</td>
        </tr>
        <tr>
          <td>first-name</td>
          <td>First Name</td>
        </tr>
        <tr>
          <td>last-name</td>
          <td>Last Name</td>
        </tr>
      </tbody>
    </table>

=== "CONSTANT_CASE"

    <table>
      <thead>
        <tr>
          <th>Technical Name</th>
          <th>Generated Label</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>PRODUCT_ID</td>
          <td>Product Id</td>
        </tr>
        <tr>
          <td>FIRST_NAME</td>
          <td>First Name</td>
        </tr>
        <tr>
          <td>LAST_NAME</td>
          <td>Last Name</td>
        </tr>
      </tbody>
    </table>

=== "snake_case"

    <table>
      <thead>
        <tr>
          <th>Technical Name</th>
          <th>Generated Label</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>product_id</td>
          <td>Product Id</td>
        </tr>
        <tr>
          <td>first_name</td>
          <td>First Name</td>
        </tr>
        <tr>
          <td>last_name</td>
          <td>Last Name</td>
        </tr>
      </tbody>
    </table>

### Use Metadata Labels

If you have a **com.sap.vocabularies.Common.v1.Label** annotation or a **sap:label** extension for your `EntityType` properties in the OData V2 metadata, you can utilize them as labels for the auto-generated form elements.

!!! warning

    If the value is set to **true** using the setter method and the labels are not found in the metadata, the [Entry Create](#entry-create) class will generate the labels in accordance with the instructions set out in the [Label Generation](#label-generation) section.

=== "Setter (setUseMetadataLabels)"

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
          <td>useMetadataLabels</td>
          <td><code>boolean</code></td>
          <td>Yes</td>
          <td>If the value is <strong>true</strong>, the OData V2 metadata labels will be used when creating labels for the auto-generated form elements</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getUseMetadataLabels)"

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
          <td>Returns the value that was set using <strong>setUseMetadataLabels()</strong> method. Default value is <strong>false</strong></td>
        </tr>
      </tbody>
    </table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 16"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // The OData V2 metadata labels will be used for the form elements.
        entry.setUseMetadataLabels(true);

        entry.createNewEntry(); 
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20"
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
              const entry = new EntryCreateCL(this, "Products");

              // The OData V2 metadata labels will be used for the form elements.
              entry.setUseMetadataLabels(true);

              entry.createNewEntry(); 
            }
          });

        });
    ```

## Resource Bundle Prefix

To modify the default resource bundle prefix utilized in the text lookup process outlined in the [Resource Model (i18n)](#resource-model-i18n), the **setResourceBundlePrefix()** method can be utilized.

!!! tip 

    If you do not wish to utilize a prefix, it is recommended that you pass an empty string, represented by `""`, as a parameter to the **setResourceBundlePrefix()** method.

=== "Setter (setResourceBundlePrefix)"

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
          <td>prefix</td>
          <td><code>string</code></td>
          <td>Yes</td>
          <td>The prefix that is used for text lookup in the <strong>i18n</strong> file of the application</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getResourceBundlePrefix)"

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
          <td>Returns the value that was set using <strong>setResourceBundlePrefix()</strong> method. Default value is <strong>antares</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

    Let us assume that you have created an `EntitySet` named **Products**, which contains the following properties: `productID` and `productName`. If you have set the resource bundle prefix to `myPrefix`, the [Entry Create](#entry-create) class will search for the following texts in the application's **i18n** file:

```properties title="i18n.properties"
myPrefixProductsproductID=Label of the productID property
myPrefixProductsproductName=Label of the productName property
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 16 25"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // New i18n text lookup format will be => myPrefix + entityPath + propertyName
        entry.setResourceBundlePrefix("myPrefix");

        entry.createNewEntry(); 
      }

      public async onCreateCategory() {
        const entry = new EntryCreateCL(this, "Categories");

        // New i18n text lookup format will be => entityPath + propertyName
        entry.setResourceBundlePrefix("");

        entry.createNewEntry();
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20 29"
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
              const entry = new EntryCreateCL(this, "Products");

              // New i18n text lookup format will be => myPrefix + entityPath + propertyName
              entry.setResourceBundlePrefix("myPrefix");

              entry.createNewEntry(); 
            },

            onCreateCategory: async function () {
              const entry = new EntryCreateCL(this, "Categories");

              // New i18n text lookup format will be => entityPath + propertyName
              entry.setResourceBundlePrefix("");

              entry.createNewEntry();          
            }
          });

        });
    ```

## Naming Strategy

To modify the default naming strategy employed during label generation, as outlined in the [Label Generation From The Technical Names](#label-generation-from-the-technical-names) documentation, the **setNamingStrategy()** method can be utilized.

=== "Setter (setNamingStrategy)"

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
          <td>strategy</td>
          <td><a href="#namingstrategies-enum">NamingStrategies</a></td>
          <td>Yes</td>
          <td>The naming strategy that is used for label generation</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getNamingStrategy)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><a href="#namingstrategies-enum">NamingStrategies</a></td>
          <td>Returns the value that was set using <strong>setNamingStrategy()</strong> method. Default value is <strong>CAMEL_CASE</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

    Let us consider the following scenario: You have an `EntitySet` named **Products** with the properties `product_id` and `product_name`. You have opted not to utilize the labels from the metadata or the i18n file. Instead, you would like the library to generate the labels. To achieve this, you can set the naming strategy as follows:

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 17"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import { NamingStrategies } from "ui5/antares/types/entry/enums"; // Import the enum

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Set the naming strategy to snake_case
        entry.setNamingStrategy(NamingStrategies.SNAKE_CASE);

        entry.createNewEntry(); 
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 4 13 24"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryCreateCL", // Import the class
        "ui5/antares/types/entry/enums" // Import the enums
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryCreateCL, EntryEnums) {
          "use strict";

          // Destructure the object to retrieve the NamingStrategies enum
          const { NamingStrategies } = EntryEnums;

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              const entry = new EntryCreateCL(this, "Products");

              // Set the naming strategy to snake_case
              entry.setNamingStrategy(NamingStrategies.SNAKE_CASE);

              entry.createNewEntry(); 
            }
          });

        });
    ```

### NamingStrategies Enum

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>NamingStrategies.CAMEL_CASE</td>
      <td><code>EntityType</code> properties use <strong>camelCase</strong> naming convention</td>
    </tr>
    <tr>
      <td>NamingStrategies.PASCAL_CASE</td>
      <td><code>EntityType</code> properties use <strong>PascalCase</strong> naming convention</td>
    </tr>
    <tr>
      <td>NamingStrategies.KEBAB_CASE</td>
      <td><code>EntityType</code> properties use <strong>kebab-case</strong> naming convention</td>
    </tr>
    <tr>
      <td>NamingStrategies.CONSTANT_CASE</td>
      <td><code>EntityType</code> properties use <strong>CONSTANT_CASE</strong> naming convention</td>
    </tr>
    <tr>
      <td>NamingStrategies.SNAKE_CASE</td>
      <td><code>EntityType</code> properties use <strong>snake_case</strong> naming convention</td>
    </tr>
  </tbody>
</table>

## Form Type

[INPUT_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.Input
[DATEPICKER_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.DatePicker
[DATETIMEPICKER_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.DateTimePicker
[CHECKBOX_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.CheckBox
[VALUELIST_ANNOTATION_URL]: https://sap.github.io/odata-vocabularies/vocabularies/Common.html#ValueListType

The [createNewEntry()](#create-new-entry) method automatically generates a [sap.ui.comp.smartform.SmartForm][SMARTFORM_URL] with [sap.ui.comp.smartfield.SmartField][SMARTFIELD_URL] content. This form type offers several advantages.

1) The form fields are rendered as Input, DatePicker, DateTimePicker, ComboBox, CheckBox, and so on, based on the **EDM type** of the `EntityType` property.

2) When the `EntityType` property is annotated with [com.sap.vocabularies.Common.v1.ValueList][VALUELIST_ANNOTATION_URL], the Value Help List is automatically added to the smart fields.

The UI5 Antares is capable of creating a [sap.ui.layout.form.SimpleForm][SIMPLEFORM_URL] with [sap.m.Input][INPUT_URL], [sap.m.DatePicker][DATEPICKER_URL], [sap.m.DateTimePicker][DATETIMEPICKER_URL], and [sap.m.CheckBox][CHECKBOX_URL] content based on the **EDM types**.

!!! danger "Attention"

    The [Value Help][VALUEHELP_CLASS_URL] class is only available when the form type is set to **SIMPLE**.

**Rendered Controls for SIMPLE Form Type**

<table>
  <thead>
    <tr>
      <th>EDM Type</th>
      <th>Control</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>Boolean</code></td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.CheckBox" target="_blank">sap.m.CheckBox</a></td>
      <td></td>
    </tr>
    <tr>
      <td><code>DateTime</code></td>
      <td>
        <a href="https://sapui5.hana.ondemand.com/#/api/sap.m.DatePicker" target="_blank">sap.m.DatePicker</a> or 
        <a href="https://sapui5.hana.ondemand.com/#/api/sap.m.DateTimePicker" target="_blank">sap.m.DateTimePicker</a>
      </td>
      <td>If the property has <strong>sap:display-format="Date"</strong> extension, it is rendered as 
        <a href="https://sapui5.hana.ondemand.com/#/api/sap.m.DatePicker" target="_blank">sap.m.DatePicker</a>, 
        otherwise as <a href="https://sapui5.hana.ondemand.com/#/api/sap.m.DateTimePicker" target="_blank">sap.m.DateTimePicker</a>
      </td>
    </tr>
    <tr>
      <td><code>DateTimeOffset</code></td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.DateTimePicker" target="_blank">sap.m.DateTimePicker</a></td>
      <td></td>
    </tr>
    <tr>
      <td><code>Others</code></td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.Input" target="_blank">sap.m.Input</a></td>
      <td>If the EDM type of the property is <strong>Edm.Decimal</strong>, precision and scale constraints are added into the input</td>
    </tr>
  </tbody>
</table>

To modify the default form type, the setFormType() method can be utilized.

=== "Setter (setFormType)"

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
          <td>formType</td>
          <td><a href="#formtypes-enum">FormTypes</a></td>
          <td>Yes</td>
          <td>The form type that is generated</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getFormType)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><a href="#formtypes-enum">FormTypes</a></td>
          <td>Returns the value that was set using <strong>setFormType()</strong> method. Default value is <strong>SMART</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

    If you have an `EntitySet` named **Products** and wish to create a simple form within the dialog, you can set the form type as follows.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 17"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import { FormTypes } from "ui5/antares/types/entry/enums"; // Import the enum

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Set the form type to SIMPLE
        entry.setFormType(FormTypes.SIMPLE);

        entry.createNewEntry(); 
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 4 13 24"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryCreateCL", // Import the class
        "ui5/antares/types/entry/enums" // Import the enums
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryCreateCL, EntryEnums) {
          "use strict";

          // Destructure the object to retrieve the FormTypes enum
          const { FormTypes } = EntryEnums;

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              const entry = new EntryCreateCL(this, "Products");

              // Set the form type to SIMPLE
              entry.setFormType(FormTypes.SIMPLE);

              entry.createNewEntry(); 
            }
          });

        });
    ```

### FormTypes Enum

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>FormTypes.SMART</td>
      <td>SmartForm with SmartField content is generated</td>
    </tr>
    <tr>
      <td>FormTypes.SIMPLE</td>
      <td>SimpleForm with Input, DatePicker, DateTimePicker, CheckBox content is generated</td>
    </tr>
  </tbody>
</table>

## Form Title

The generated form's title is automatically generated by combining the words **Create New** with the `entityPath` defined in the [constructor](#constructor). For example, if the `entityPath` is set to **Products**, the title will be **Create New Products**.

To modify the default form title, please utilize the **setFormTitle()** method.

=== "Setter (setFormTitle)"

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
          <td>title</td>
          <td><code>string</code></td>
          <td>Yes</td>
          <td>The generated form's title</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getFormTitle)"

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
          <td>Returns the value that was set using <strong>setFormTitle()</strong> method. Default value is <strong>Create new ${entityPath}</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

    Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 16"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Set the form title
        entry.setFormTitle("My Form Title");

        entry.createNewEntry(); 
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20"
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
              const entry = new EntryCreateCL(this, "Products");

              // Set the form title
              entry.setFormTitle("My Form Title");

              entry.createNewEntry(); 
            }
          });

        });
    ```

![Form Title](./images/create_entry/form_title.png)

## Form Grouping

By default, all the properties of an `EntitySet` are placed in a single group or section in the auto-generated dialog or object page, with the title for that group hidden (visible on the object page). It is possible to categorize the properties into different groups in the auto-generated form, should you wish to do so.

!!! info

    Upon activation of the [Object Page](#object-page) feature, the form grouping feature generates sections on the object page.

To create the form groups or object page sections, **setFormGroups()** method can be utilized.

!!! info

	- All of the **key** properties of the `EntitySet` are placed into a default group, and this behavior is not open to modification. The title of this group can be modified using the **setDefaultGroupTitle()** method. If the **setDefaultGroupTitle()** method is not used, the default group title will remain hidden in the auto-generated dialog. However, it will always be visible in the auto-generated object page, and the title will be derived from the [Form Title](#form-title) feature for the object page.

	- Any properties not included in the **setFormGroups()** method or the default group are placed in a group designated as the **Unknown Group**. To disable this group, set the second parameter of the **setFormGroups()** method to false. This configuration allows only the key properties and the other properties specified in the **setFormGroups()** method to be visible in the auto-generated dialog or auto-generated object page.

	- Should you wish to retain the **Unknown Group** but modify the group title, you may utilize the **setUnknownGroupTitle()** method.

=== "Setter (setFormGroups)"

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
	      <td>groups</td>
	      <td><a href="#iformgroups-type-definition">IFormGroups[]</a></td>
	      <td>Yes</td>
	      <td>The form groups or sections displayed in the auto-generated dialog or object page</td>
	    </tr>
	    <tr>
	      <td>includeAllProperties?</td>
	      <td><code>boolean</code></td>
	      <td>No</td>
	      <td>If set to <strong>false</strong> all the other <strong>non-key</strong> properties will not be included. Default is true</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getFormGroups)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td><a href="#iformgroups-type-definition">IFormGroups[]</a></td>
	      <td>Returns the groups that were set using <strong>setFormGroups()</strong> method. Default value is <strong>[]</strong></td>
	    </tr>
	  </tbody>
	</table>

---

=== "Setter (setDefaultGroupTitle)"

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
	      <td>title</td>
	      <td><code>string</code></td>
	      <td>Yes</td>
	      <td>The title of the default group or section that is generated for the <strong>key</strong> properties</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getDefaultGroupTitle)"

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
	      <td>Returns the title that was set using <strong>setDefaultGroupTitle()</strong> method. Default value is <strong>undefined</strong> for the dialog. However, it is derived from the <a 	href="#form-title">Form Title</a> for the object page</td>
	    </tr>
	  </tbody>
	</table>

---

=== "Setter (setUnknownGroupTitle)"

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
	      <td>title</td>
	      <td><code>string</code></td>
	      <td>Yes</td>
	      <td>The title of the unknown group or section that is generated for the <strong>other</strong> properties</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getUnknownGroupTitle)"

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
	      <td>Returns the title that was set using <strong>setUnknownGroupTitle()</strong> method. Default value is <strong>Unknown Group</strong></td>
	    </tr>
	  </tbody>
	</table>

!!! example

	Let us consider an `EntitySet` named **Products** and the objective is to categorize the properties into different groups in the auto-generated form. Please see the results below after the code blocks for reference.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 17-23 26 29"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public onCreateProduct() {
	    // initialize
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // set the form groups and include all the other properties
	    entry.setFormGroups([{
	      title: "My Group 1",
	      properties: ["name", "description"]
	    },{
	      title: "My Group 2",
	      properties: ["brand", "price", "currency"]
	    }]);

	    // set the default group title
	    entry.setDefaultGroupTitle("My Default Group");

	    // set the unknown group title
	    entry.setUnknownGroupTitle("My Unknown Group");

	    // call the dialog
	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 21-27 30 33"
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
	          // initialize
	          const entry = new EntryCreateCL(this, "Products");

	          // set the form groups and include all the other properties
	          entry.setFormGroups([{
	            title: "My Group 1",
	            properties: ["name", "description"]
	          },{
	            title: "My Group 2",
	            properties: ["brand", "price", "currency"]
	          }]);

	          // set the default group title
	          entry.setDefaultGroupTitle("My Default Group");

	          // set the unknown group title
	          entry.setUnknownGroupTitle("My Unknown Group");

	          // call the dialog
	          entry.createNewEntry(); 
	        }

	      });

	    });
	```

![Form Grouping](./images/create_entry/form_grouping_1.png)

### IFormGroups Type Definition

<table>
  <thead>
    <tr>
      <th>Property</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>IFormGroups</td>
      <td><code>object</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&emsp;title</td>
      <td><code>string</code></td>
      <td>The title of the form group or object page section</td>
    </tr>
    <tr>
      <td>&emsp;properties</td>
      <td><code>string[]</code></td>
      <td>The properties that will be included into the group</td>
    </tr>
  </tbody>
</table>

## Custom Data

[CUSTOM_DATA_URL]: https://sapui5.hana.ondemand.com/sdk/#/api/sap.ui.core.CustomData

The UI5 Antares enables users to add [Custom Data][CUSTOM_DATA_URL] to the auto-generated SIMPLE/SMART form elements. To add custom data, simply use the **setFieldCustomData()** method.

=== "Setter (setFieldCustomData)"

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
	      <td>customData</td>
	      <td><a href="#ifieldcustomdata-type-definition">IFieldCustomData[]</a></td>
	      <td>Yes</td>
	      <td>The custom data that will be added to the auto-generated form elements</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getFieldCustomData)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td><a href="#ifieldcustomdata-type-definition">IFieldCustomData[]</a></td>
	      <td>Returns the value that was set using <strong>setFieldCustomData()</strong> method. Default value is <strong>[]</strong></td>
	    </tr>
	  </tbody>
	</table>

**Example**

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 18-24"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import CustomData from "sap/ui/core/CustomData"; // Import the custom data

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public onCreateProduct() {
	    // initialize
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // set the custom data
	    entry.setFieldCustomData([{
	      propertyName: "name",
	      customData: new CustomData({key: "MyKey1", value:"MyValue1"})
	    },{
	      propertyName: "description",
	      customData: new CustomData({key: "MyKey2", value:"MyValue2"})
	    }]);

	    // call the dialog
	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 22-28"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "sap/ui/core/CustomData" // Import the custom data
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, CustomData) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          // initialize
	          const entry = new EntryCreateCL(this, "Products");

	          // set the custom data
	          entry.setFieldCustomData([{
	            propertyName: "name",
	            customData: new CustomData({key: "MyKey1", value:"MyValue1"})
	          },{
	            propertyName: "description",
	            customData: new CustomData({key: "MyKey2", value:"MyValue2"})
	          }]);

	          // call the dialog
	          entry.createNewEntry(); 
	        }

	      });

	    });
	```

### IFieldCustomData Type Definition

<table>
  <thead>
    <tr>
      <th>Property</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>IFieldCustomData</td>
      <td><code>object</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&emsp;propertyName</td>
      <td><code>string</code></td>
      <td>The name of the property that will have the custom data</td>
    </tr>
    <tr>
      <td>&emsp;customData</td>
      <td><a href="https://sapui5.hana.ondemand.com/sdk/#/api/sap.ui.core.CustomData">Custom Data</a></td>
      <td>The custom data</td>
    </tr>
  </tbody>
</table>

## Text In Edit Mode Source

[TEXT_IN_EDIT_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.comp.smartfield.TextInEditModeSource

The UI5 Antares allows users to set the `textInEditModeSource` property of the [SmartField][SMARTFIELD_URL] when a [SmartForm][SMARTFORM_URL] is generated. To set the `textInEditModeSource` property, the **setTextInEditModeSource()** method can be utilized.

=== "Setter (setTextInEditModeSource)"

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
	      <td>textInEditModeSource</td>
	      <td><a href="#itextineditmodesource-type-definition">ITextInEditModeSource[]</a></td>
	      <td>Yes</td>
	      <td>The <code>textInEditModeSource</code> configs for the properties</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getTextInEditModeSource)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td><a href="#itextineditmodesource-type-definition">ITextInEditModeSource[]</a></td>
	      <td>Returns the value that was set using <strong>setTextInEditModeSource()</strong> method. Default value is <strong>[]</strong></td>
	    </tr>
	  </tbody>
	</table>

**Example**

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 18-24"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import { smartfield } from "sap/ui/comp/library"; // Import the smartfield library

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public onCreateProduct() {
	    // initialize
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // set the textInEditModeSource
	    entry.setTextInEditModeSource([{
	      propertyName: "name",
	      textInEditModeSource: smartfield.TextInEditModeSource.NavigationProperty
	    },{
	      propertyName: "description",
	      customData: smartfield.TextInEditModeSource.ValueList
	    }]);

	    // call the dialog
	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 12 24-30"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "sap/ui/comp/library" // Import the comp library
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, UIComp) {
	      "use strict";

	      const { TextInEditModeSource } = UIComp["smartfield"]; // Destructure to get the enum

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          // initialize
	          const entry = new EntryCreateCL(this, "Products");

	          // set the textInEditModeSource
	          entry.setTextInEditModeSource([{
	            propertyName: "name",
	            textInEditModeSource: TextInEditModeSource.NavigationProperty
	          },{
	            propertyName: "description",
	            customData: TextInEditModeSource.ValueList
	          }]);

	          // call the dialog
	          entry.createNewEntry(); 
	        }

	      });

	    });
	```

### ITextInEditModeSource Type Definition

<table>
  <thead>
    <tr>
      <th>Property</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ITextInEditModeSource</td>
      <td><code>object</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&emsp;propertyName</td>
      <td><code>string</code></td>
      <td>The name of the property whose <code>textInEditModeSource</code> property will be set</td>
    </tr>
    <tr>
      <td>&emsp;textInEditModeSource</td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.comp.smartfield.TextInEditModeSource">TextInEditModeSource</a></td>
      <td>The <code>textInEditModeSource</code> property of the smartfield</td>
    </tr>
  </tbody>
</table>

## Begin Button Text

Upon clicking the `Begin Button`, the [Entry Create](#entry-create) class initiates the validation process and submits the transient entity through the OData V2 model. The default text displayed on the Begin button is **Create**. The text displayed on the `Begin Button` can be modified using the **setBeginButtonText()** method.

=== "Setter (setBeginButtonText)"

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
	      <td>text</td>
	      <td><code>string</code></td>
	      <td>Yes</td>
	      <td>The text displayed on the begin button</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getBeginButtonText)"

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
	      <td>Returns the value that was set using <strong>setBeginButtonText()</strong> method. Default value is <strong>Create</strong></td>
	    </tr>
	  </tbody>
	</table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 16"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL(this, "Products");

	    // Set the begin button text
	    entry.setBeginButtonText("My Begin Button Text");

	    entry.createNewEntry(); 
	  }
	}
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 20"
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
	          const entry = new EntryCreateCL(this, "Products");

	          // Set the begin button text
	          entry.setBeginButtonText("My Begin Button Text");

	          entry.createNewEntry(); 
	        }
	      });

	    });
	```

![Begin Button Text](./images/create_entry/begin_button_text.png)

## Begin Button Type

[BUTTON_TYPE_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType

The default type used on the `Begin Button` is [ButtonType.Success][BUTTON_TYPE_URL]. Should you wish to modify the default Begin Button type, please utilize the **setBeginButtonType()** method.

=== "Setter (setBeginButtonType)"

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
	      <td>type</td>
	      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType">ButtonType</a></td>
	      <td>Yes</td>
	      <td>The type of the begin button</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getBeginButtonType)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType">ButtonType</a></td>
	      <td>Returns the value that was set using <strong>setBeginButtonType()</strong> method. Default value is <strong>Success</strong></td>
	    </tr>
	  </tbody>
	</table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 17"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import { ButtonType } from "sap/m/library"; // Import the ButtonType enum

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL(this, "Products");

	    // Set the begin button type
	    entry.setBeginButtonType(ButtonType.Attention);

	    entry.createNewEntry(); 
	  }
	}
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 21"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "sap/m/ButtonType" // Import the ButtonType enum
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, ButtonType) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Set the begin button type
	          entry.setBeginButtonType(ButtonType.Attention);

	          entry.createNewEntry(); 
	        }
	      });

	    });
	```

![Begin Button Type](./images/create_entry/begin_button_type.png)

## End Button Text

Once the end user clicks the `End Button`, the [Entry Create](#entry-create) class resets the transient entity through the OData V2 Model and destroys the created dialog. The default text displayed on the end button is **Close**. This text can be modified using the **setEndButtonText()** method.

=== "Setter (setEndButtonText)"

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
	      <td>text</td>
	      <td><code>string</code></td>
	      <td>Yes</td>
	      <td>The text displayed on the end button</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getEndButtonText)"

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
	      <td>Returns the value that was set using <strong>setEndButtonText()</strong> method. Default value is <strong>Close</strong></td>
	    </tr>
	  </tbody>
	</table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 16"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL(this, "Products");

	    // Set the end button text
	    entry.setEndButtonText("My End Button Text");

	    entry.createNewEntry(); 
	  }
	}
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 20"
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
	          const entry = new EntryCreateCL(this, "Products");

	          // Set the end button text
	          entry.setEndButtonText("My End Button Text");

	          entry.createNewEntry(); 
	        }
	      });

	    });
	```

![End Button Text](./images/create_entry/end_button_text.png)

## End Button Type

The default type used on the `End Button` is [ButtonType.Negative][BUTTON_TYPE_URL]. Should you wish to modify the default end button type, please utilize the **setEndButtonType()** method.

=== "Setter (setEndButtonType)"

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
	      <td>type</td>
	      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType">ButtonType</a></td>
	      <td>Yes</td>
	      <td>The type of the end button</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getEndButtonType)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.m.ButtonType">ButtonType</a></td>
	      <td>Returns the value that was set using <strong>setEndButtonType()</strong> method. Default value is <strong>Negative</strong></td>
	    </tr>
	  </tbody>
	</table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 17"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import { ButtonType } from "sap/m/library"; // Import the ButtonType enum

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL(this, "Products");

	    // Set the end button type
	    entry.setEndButtonType(ButtonType.Transparent);

	    entry.createNewEntry(); 
	  }
	}
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 21"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "sap/m/ButtonType" // Import the ButtonType enum
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, ButtonType) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Set the end button type
	          entry.setEndButtonType(ButtonType.Transparent);

	          entry.createNewEntry(); 
	        }
	      });

	    });
	```

![End Button Type](./images/create_entry/end_button_type.png)

## Properties with Edm.Guid Type

The [Entry Create](#entry-create) class automatically generates unique, randomly generated UUID values for the **key** properties (with `Edm.Guid` type) of the `EntityType`, which are then hidden from view on the form for the end user.

Should you wish to modify the default random UUID generation behavior, please utilize the **setGenerateRandomGuid()** method.

=== "Setter (setGenerateRandomGuid)"

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
	      <td>strategy</td>
	      <td><a href="#guidstrategies-enum">GuidStrategies</a></td>
	      <td>Yes</td>
	      <td>The random UUID generation strategy</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getGenerateRandomGuid)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td><a href="#guidstrategies-enum">GuidStrategies</a></td>
	      <td>Returns the value that was set using <strong>setGenerateRandomGuid()</strong> method. Default value is <strong>ONLY_KEY</strong></td>
	    </tr>
	  </tbody>
	</table>

To modify the default visibility behavior of the properties with `Edm.Guid` type, please utilize the **setDisplayGuidProperties()** method.

=== "Setter (setDisplayGuidProperties)"

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
	      <td>strategy</td>
	      <td><a href="#guidstrategies-enum">GuidStrategies</a></td>
	      <td>Yes</td>
	      <td>The visibility strategy for the properties with <code>Edm.Guid</code> type</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getDisplayGuidProperties)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td><a href="#guidstrategies-enum">GuidStrategies</a></td>
	      <td>Returns the value that was set using <strong>setDisplayGuidProperties()</strong> method. Default value is <strong>ONLY_NON_KEY</strong></td>
	    </tr>
	  </tbody>
	</table>

!!! example

	Let us consider the following scenario: You have an `EntitySet` named **Products** with `ID`, `categoryID`, and `supplierID`, all of which have the `Edm.Guid` type. You would like to allow the end user to view all `Edm.Guid` properties and have the library generate random UUID values only for the **non-key** properties.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 17 20"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import { GuidStrategies } from "ui5/antares/types/entry/enums"; // Import the GuidStrategies enum

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL(this, "Products");

	    // Let the end user to display all the properties with Edm.Guid type
	    entry.setDisplayGuidProperties(GuidStrategies.ALL);

	    // Have the library generate random UUID values only for the non-key properties
	    entry.setGenerateRandomGuid(GuidStrategies.ONLY_NON_KEY);

	    entry.createNewEntry(); 
	  }
	}
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 13 24 27"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "ui5/antares/types/entry/enums" // Import the enums
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, EntryEnums) {
	      "use strict";

	      // Destructure the object to retrieve the GuidStrategies enum
	      const { GuidStrategies } = EntryEnums;

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Let the end user to display all the properties with Edm.Guid type
	          entry.setDisplayGuidProperties(GuidStrategies.ALL);

	          // Have the library generate random UUID values only for the non-key properties
	          entry.setGenerateRandomGuid(GuidStrategies.ONLY_NON_KEY);

	          entry.createNewEntry(); 
	        }
	      });

	    });
	```

![Guid Strategy](./images/create_entry/guid_strategy.png)

### GuidStrategies Enum

!!! info

	Please note that if a random UUID is generated for a property and marked as visible, this field **cannot be edited** by the end user.

=== "setGenerateRandomGuid()"

	<table>
	  <thead>
	    <tr>
	      <th>Name</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td>GuidStrategies.ALL</td>
	      <td>Generate random UUID values for all the properties with <code>Edm.Guid</code> type</td>
	    </tr>
	    <tr>
	      <td>GuidStrategies.ONLY_KEY</td>
	      <td>Generate random UUID values only for the <strong>key</strong> properties with <code>Edm.Guid</code> type</td>
	    </tr>
	    <tr>
	      <td>GuidStrategies.ONLY_NON_KEY</td>
	      <td>Generate random UUID values only for the properties that are <strong>not key</strong> and have <code>Edm.Guid</code> type</td>
	    </tr>
	    <tr>
	      <td>GuidStrategies.NONE</td>
	      <td>No random UUID generation</td>
	    </tr>
	  </tbody>
	</table>

=== "setDisplayGuidProperties()"

	<table>
	  <thead>
	    <tr>
	      <th>Name</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td>GuidStrategies.ALL</td>
	      <td>The end user can display all the properties with <code>Edm.Guid</code> type</td>
	    </tr>
	    <tr>
	      <td>GuidStrategies.ONLY_KEY</td>
	      <td>The end user can display only the <strong>key</strong> properties with <code>Edm.Guid</code> type</td>
	    </tr>
	    <tr>
	      <td>GuidStrategies.ONLY_NON_KEY</td>
	      <td>The end user can display only the properties that are <strong>not key</strong> and have <code>Edm.Guid</code> type</td>
	    </tr>
	    <tr>
	      <td>GuidStrategies.NONE</td>
	      <td>No property with <code>Edm.Guid</code> is visible to the end user</td>
	    </tr>
	  </tbody>
	</table>

## Form Property Order

The auto-generated form elements are displayed in accordance with the order set forth in the OData V2 metadata.

!!! note

    Please be advised that the **key** properties always come **first** on the auto-generated form. This behavior is not open to modification.

The order of the **non-key** properties can be modified using the **setPropertyOrder()** method.

=== "Setter (setPropertyOrder)"

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
          <td>order</td>
          <td><code>string[]</code></td>
          <td>Yes</td>
          <td>The order of the <strong>non-key</strong> properties that will be placed into the auto-generated form will be in the same order as this parameter</td>
        </tr>
        <tr>
          <td>useAllProperties?</td>
          <td><code>boolean</code></td>
          <td>No</td>
          <td>If the value is set to <strong>false</strong>, only the <strong>key</strong> properties and the properties specified in the <strong>order</strong> parameter will be visible to the end user</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getPropertyOrder)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>string[]</code></td>
          <td>Returns the value that was set using <strong>setPropertyOrder()</strong> method. Default value is <strong>[]</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 15 24"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Set the property order and bring all the properties
        entry.setPropertyOrder(["categoryID", "supplierID", "price", "currency", "name"]);

        entry.createNewEntry(); 
      }

      public async onCreateCustomer() {
        const entry = new EntryCreateCL(this, "Customers");

        // Set the property order and exclude the other properties
        entry.setPropertyOrder(["country", "name"], false);

        entry.createNewEntry();     
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20 29"
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
              const entry = new EntryCreateCL(this, "Products");

              // Set the property order and bring all the properties
              entry.setPropertyOrder(["categoryID", "supplierID", "price", "currency", "name"]);

              entry.createNewEntry(); 
            },

            onCreateCustomer: async function () {
              const entry = new EntryCreateCL(this, "Customers");

              // Set the property order and exclude the other properties
              entry.setPropertyOrder(["country", "name"], false);

              entry.createNewEntry();     
            }
          });

        });
    ```

<table>
  <thead>
    <tr>
      <th>Before</th>
      <th>After</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><img src="../images/create_entry/before_property_order.png" alt="Before Property Order"></td>
      <td><img src="../images/create_entry/after_property_order.png?raw=true" alt="After Property Order"></td>
    </tr>
  </tbody>
</table>

## Excluded Properties

By default, all `EntityType` properties of the `EntitySet` specified in the [class constructor](#constructor) are accessible to the end user.

To exclude properties from the auto-generated form, please utilize the **setExcludedProperties()** method. Please note that it is still possible to set initial values for excluded properties through the [createNewEntry()](#create-new-entry) method's parameter.

!!! danger "Attention"

    It is not possible to exclude any of the **key** properties.

=== "Setter (setExcludedProperties)"

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
          <td>properties</td>
          <td><code>string[]</code></td>
          <td>Yes</td>
          <td>The properties that will be excluded from the auto-generated form</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getExcludedProperties)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>string[]</code></td>
          <td>Returns the value that was set using <strong>setExcludedProperties()</strong> method. Default value is <strong>[]</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 16"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Set the excluded properties
        entry.setExcludedProperties(["categoryID", "supplierID"]);

        entry.createNewEntry(); 
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20"
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
              const entry = new EntryCreateCL(this, "Products");

              // Set the excluded properties
              entry.setExcludedProperties(["categoryID", "supplierID"]);

              entry.createNewEntry(); 
            }
          });

        });
    ```

<table>
  <thead>
    <tr>
      <th>Before</th>
      <th>After</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><img src="../images/create_entry/before_excluded_properties.png?raw=true" alt="Before Excluded Properties" style="max-width: 100%; height: auto;"></td>
      <td><img src="../images/create_entry/after_excluded_properties.png?raw=true" alt="After Excluded Properties" style="max-width: 100%; height: auto;"></td>
    </tr>
  </tbody>
</table>

## Mandatory Properties

The [Entry Create](#entry-create) class includes a built-in validation mechanism that checks the mandatory properties and applies the relevant [Validation Logic](#validation-logic) before submitting the transient entity.

!!! info

    The default setting marks all **key** properties and properties with the `Nullable=false` attribute as mandatory.

In order to include properties in the mandatory check mechanism, the **setMandatoryProperties()** method can be utilized.

=== "Setter (setMandatoryProperties)"

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
          <td>properties</td>
          <td><code>string[]</code></td>
          <td>Yes</td>
          <td>The properties that will be included into the mandatory check mechanism</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getMandatoryProperties)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>string[]</code></td>
          <td>Returns the value that was set using <strong>setMandatoryProperties()</strong> method. Default value is <strong>[]</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 16"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Set the mandatory properties
        entry.setMandatoryProperties(["name", "description"]);

        entry.createNewEntry(); 
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20"
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
              const entry = new EntryCreateCL(this, "Products");

              // Set the mandatory properties
              entry.setMandatoryProperties(["name", "description"]);

              entry.createNewEntry(); 
            }
          });

        });
    ```

<table>
  <thead>
    <tr>
      <th>Before</th>
      <th>After</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><img src="../images/create_entry/before_mandatory_properties.png?raw=true" alt="Before Mandatory Properties" style="max-width: 100%; height: auto;"></td>
      <td><img src="../images/create_entry/after_mandatory_properties.png?raw=true" alt="After Mandatory Properties" style="max-width: 100%; height: auto;"></td>
    </tr>
  </tbody>
</table>

### Mandatory Error Message

[MESSAGEBOX_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.MessageBox

In the event that a property fails the mandatory check mechanism, the value state of the UI control (SmartField, Input, etc.) is set to `Error`, and a default message is displayed in the [sap.m.MessageBox.error][MESSAGEBOX_URL] to the end user.

!!! info

    **Default Message:** Please fill in all required fields.

To customize the default error message, please utilize the **setMandatoryErrorMessage()** method.

=== "Setter (setMandatoryErrorMessage)"

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
          <td>The displayed message when the mandatory check mechanism fails</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getMandatoryErrorMessage)"

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
          <td>Returns the value that was set using <strong>setMandatoryErrorMessage()</strong> method. Default value is <strong>Please fill in all required fields.</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 16"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Set the mandatory error message
        entry.setMandatoryErrorMessage("My Mandatory Error Message");

        entry.createNewEntry(); 
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20"
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
              const entry = new EntryCreateCL(this, "Products");

              // Set the mandatory error message
              entry.setMandatoryErrorMessage("My Mandatory Error Message");

              entry.createNewEntry(); 
            }
          });

        });
    ```

![Mandatory Error Message](./images/create_entry/mandatory_error_message.png)

## Readonly Properties

In the event that the property type is `Edm.Guid` and the library generates a random UUID for it, the end user will be unable to edit it.

!!! tip

    For additional details regarding properties with an `Edm.Guid` type, please refer to the [Properties with Edm.Guid Type](#properties-with-edmguid-type) section.

To prevent end users from editing certain properties, the **setReadonlyProperties()** method can be utilized.

!!! info

    It is possible to set the initial values for readonly properties.

=== "Setter (setReadonlyProperties)"

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
          <td>properties</td>
          <td><code>string[]</code></td>
          <td>Yes</td>
          <td>The read-only properties</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getReadonlyProperties)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>string[]</code></td>
          <td>Returns the value that was set using <strong>setReadonlyProperties()</strong> method. Default value is <strong>[]</strong></td>
        </tr>
      </tbody>
    </table>

!!! example

	Please refer to the result below, which can be found after the code blocks.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 16"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Set the read-only properties
        entry.setReadonlyProperties(["name", "description"]);

        entry.createNewEntry({
          name: "My Product Name",
          description: "My Product Description"
        }); 
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20"
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
              const entry = new EntryCreateCL(this, "Products");

              // Set the read-only properties
              entry.setReadonlyProperties(["name", "description"]);

              entry.createNewEntry({
                name: "My Product Name",
                description: "My Product Description"
              }); 
            }
          });

        });
    ```

![Readonly Properties](./images/create_entry/readonly_properties.png)

## Attach Submit Completed

Once the transient entity has been successfully submitted, the [Entry Create](#entry-create) class can then call a function with a specific signature. The result of the submission is then passed to the attached function.

To attach a function, **attachSubmitCompleted()** method can be utilized.

**Setter (attachSubmitCompleted)**

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
      <td>submitCompleted</td>
      <td>(response: <a href="#response-class">ResponseCL&lt;ResponseT = object&gt;</a>) =&gt; void</td>
      <td>Yes</td>
      <td>The function that will be called after the successful submit</td>
    </tr>
    <tr>
      <td>listener?</td>
      <td>object</td>
      <td>No</td>
      <td>The default listener is the <strong>controller</strong> from <a href="#constructor">constructor</a></td>
    </tr>
  </tbody>
</table>

!!! example

    Let us assume that once the submission is successful, you would like to receive a response and take the necessary actions.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 16 22-33"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import ResponseCL from "ui5/antares/entry/v2/ResponseCL"; // Import the ResponseCL class
    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL<IProducts>(this, "Products");

        // Attach the submit completed function
        entry.attachSubmitCompleted(this.productSubmitCompleted, this);

        entry.createNewEntry();
      }

      // Please use the same type for the ResponseCL generic as you did for EntryCreateCL
      private productSubmitCompleted(response: ResponseCL<IProducts>): void {
        // Get the status code. Please be aware, it may also be undefined
        const statusCode = response.getStatusCode();

        // Get the data that was submitted. Please be aware, it may also be undefined
        const submittedData = response.getResponse();

        if (submittedData) {
          // Some operations
          const createdProductID = submittedData.ID;
        }
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
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 20 25-36"
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
              const entry = new EntryCreateCL(this, "Products");

              // Attach the submit completed function
              entry.attachSubmitCompleted(this._productSubmitCompleted, this);

              entry.createNewEntry();
            },

            _productSubmitCompleted: function (response) {
              // Get the status code. Please be aware, it may also be undefined
              const statusCode = response.getStatusCode();

              // Get the data that was submitted. Please be aware, it may also be undefined
              const submittedData = response.getResponse();

              if (submittedData) {
                // Some operations
                const createdProductID = submittedData.ID;
              }          
            }
          });

        });
    ```

## Attach Submit Failed

In the event that the submission of the transient entity is unsuccessful, the [Entry Create](#entry-create) class can then call a function with a specific signature. The result of the submission will then be passed to the attached function for processing.

To attach a function, **attachSubmitFailed()** method can be utilized.

**Setter (attachSubmitFailed)**

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
      <td>submitFailed</td>
      <td>(response: <a href="#response-class">ResponseCL&lt;ISubmitResponse&gt;</a>) =&gt; void</td>
      <td>Yes</td>
      <td>The function that will be called after the submission fails</td>
    </tr>
    <tr>
      <td>listener?</td>
      <td>object</td>
      <td>No</td>
      <td>The default listener is the <strong>controller</strong> from <a href="#constructor">constructor</a></td>
    </tr>
  </tbody>
</table>

!!! example

    Let us assume that once the submission is unsuccessful, you would like to receive a response and take the necessary actions.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="3 4 5 18 24-35"
    import Controller from "sap/ui/core/mvc/Controller";
    import MessageBox from "sap/m/MessageBox";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import ResponseCL from "ui5/antares/entry/v2/ResponseCL"; // Import the ResponseCL class
    import { ISubmitResponse } from "ui5/antares/types/entry/submit"; // Import the error type
    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL<IProducts>(this, "Products");

        // Attach the submit failed function
        entry.attachSubmitFailed(this.productSubmitFailed, this);

        entry.createNewEntry();
      }

      // Please use the ISubmitResponse type for the ResponseCL generic
      private productSubmitFailed(response: ResponseCL<ISubmitResponse>): void {
        // Get the status code. Please be aware, it may also be undefined
        const statusCode = response.getStatusCode();

        // Get the response. Please be aware, it may also be undefined
        const reason = response.getResponse();

        // Get the statusText
        if (reason) {
          MessageBox.error(reason.statusText || "The product was not created!");
        }
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
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="4 21 26-37"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "sap/m/MessageBox",
        "ui5/antares/entry/v2/EntryCreateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, MessageBox, EntryCreateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              const entry = new EntryCreateCL(this, "Products");

              // Attach the submit failed function
              entry.attachSubmitFailed(this._productSubmitFailed, this);

              entry.createNewEntry();
            },

            _productSubmitFailed: function (response) {
              // Get the status code. Please be aware, it may also be undefined
              const statusCode = response.getStatusCode();

              // Get the response. Please be aware, it may also be undefined
              const reason = response.getResponse();

              // Get the statusText
              if (reason) {
                MessageBox.error(reason.statusText || "The product was not created!");
              }     
            }
          });

        });
    ```

## Response Class

Once the transient entity has been submitted, the generic **ResponseCL&lt;ResponseT = object&gt;** object is instantiated and passed to the functions attached using the [attachSubmitCompleted()](#attach-submit-completed) or [attachSubmitFailed()](#attach-submit-failed) methods.

Once the submit has been completed, the class has two public methods that can be used to retrieve information. The return type of the **getResponse()** method is dependent on the response type (success or failure).

=== "Submit Completed (getResponse)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>ResponseT</code> | <code>object</code> | <code>undefined</code></td>
          <td>Returns the data that was submitted successfully through the OData V2 Model.</td>
        </tr>
      </tbody>
    </table>

=== "Submit Failed (getResponse)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>object</code></td>
          <td></td>
        </tr>
        <tr>
          <td>&emsp;statusCode?: <code>string</code> | <code>undefined</code></td>
          <td>The status code of the HTTP request.</td>
        </tr>
        <tr>
          <td>&emsp;body?: <code>string</code> | <code>undefined</code></td>
          <td>The HTTP response body.</td>
        </tr>
        <tr>
          <td>&emsp;statusText?: <code>string</code> | <code>undefined</code></td>
          <td>The HTTP status text.</td>
        </tr>
        <tr>
          <td>&emsp;headers?: <code>object</code> | <code>undefined</code></td>
          <td>The HTTP response headers.</td>
        </tr>
      </tbody>
    </table>

**Submit Completed and Failed (getStatusCode)**

<table>
  <thead>
    <tr>
      <th>Returns</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>string</code> | <code>undefined</code></td>
      <td>Returns the status code of the HTTP request.</td>
    </tr>
  </tbody>
</table>

## Validation Logic

The UI5 Antares classes include a built-in validation mechanism to guarantee that the input provided by the end user is accurate and complete before submission through the OData V2 model.

In the event of a validation failure, the end user is promptly informed via an [sap.m.MessageBox.error][MESSAGEBOX_URL] message, and the submission remains in a pending status until the validation process is successfully completed.

There are two possible approaches to user input validation.

1) [Validation with Operator](#validation-with-operator)

2) [Validation with Validator Function](#validation-with-validator-function)

### Constructor

In order to utilise the **ValidationLogicCL** class, it is necessary to initialise an object from this class.

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
      <td>settings</td>
      <td><code>object</code></td>
      <td>Yes</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;propertyName</td>
      <td><code>string</code></td>
      <td>Yes</td>
      <td></td>
      <td>This is the property of the <code>EntitySet</code> that is set in the <a href="#constructor">constructor</a> for which the validation will be performed.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;validator?</td>
      <td><code>(value: <a href="#validation-with-validator-function">ValidatorValueParameter</a>) =&gt; boolean</code></td>
      <td>No</td>
      <td></td>
      <td>The validator function</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;listener?</td>
      <td><code>object</code></td>
      <td>No</td>
      <td></td>
      <td>The object that is bind to the validator function</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;value1?</td>
      <td><code>string</code> | <code>number</code> | <code>Date</code> | <a href="https://sapui5.hana.ondemand.com/#/api/module:sap/ui/core/date/UI5Date">UI5 Date</a></td>
      <td>No</td>
      <td></td>
      <td>First value to use with the given operator</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;value2?</td>
      <td><code>string</code> | <code>number</code> | <code>Date</code> | <a href="https://sapui5.hana.ondemand.com/#/api/module:sap/ui/core/date/UI5Date">UI5 Date</a></td>
      <td>No</td>
      <td></td>
      <td>Second value to use with the given operator, used only for the <strong>BT</strong> and <strong>NB</strong> operators</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;operator?</td>
      <td><a href="#validationoperator-enum">ValidationOperator</a></td>
      <td>No</td>
      <td></td>
      <td>Operator used for the validation</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;message?</td>
      <td><code>string</code></td>
      <td>No</td>
      <td>Validation failed for <code>propertyName</code></td>
      <td>The message that is displayed when the validation is unsuccessful</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;showMessageBox?</td>
      <td><code>boolean</code></td>
      <td>No</td>
      <td>true</td>
      <td>Indicates whether the message box should be displayed by the end user</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;invalidValueMessage?</td>
      <td><code>string</code></td>
      <td>No</td>
      <td>Invalid value for <code>propertyName</code></td>
      <td>Displayed message when the end user types in an invalid value. For instance: string to a number field</td>
    </tr>
  </tbody>
</table>

### Validation with Operator

In the event that particular values are required for user input validation purposes, these can be defined within the [constructor](#constructor_1) function.

!!! tip

	Please note that the type of the value1 and value2 parameters must align with the type of the property undergoing validation.

	As an example, when the property type is `Edm.DateTime` or `Edm.DateTimeOffset`, then the data type of the `value1` and `value2` must be either **JavaScript Date** or [UI5 Date](https://sapui5.hana.ondemand.com/#/api/module:sap/ui/core/date/UI5Date).

!!! example

	Let us consider an `EntitySet` named **Products** with the following properties: `ID`, `name`, `description`, `price`, and `currency`. We wish to implement a logic that allows the **price** to be between 1500 and 2500, and that the **currency** is limited to EUR.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 4 18-25 28-33 36 39"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import ValidationLogicCL from "ui5/antares/ui/ValidationLogicCL"; // Import the ValidationLogicCL class
	import { ValidationOperator } from "ui5/antares/types/ui/enums"; // Import the ValidationOperator enum

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // Create an object from the Validation Logic class for the price validation
	    const priceValidation = new ValidationLogicCL({
	      propertyName: "price", // price property of the Products
	      operator: ValidationOperator.BT,
	      value1: 1500,
	      value2: 2500,
	      message: "The price must be between 1500 and 2500",
	      invalidValueMessage: "Please only type number for the price field"      
	    });

	    // Create an object from the Validation Logic class for the currency validation
	    const currencyValidation = new ValidationLogicCL({
	      propertyName: "currency", // Currency property of the Products
	      operator: ValidationOperator.EQ,
	      value1: "EUR",
	      message: "Only EUR currency can be used"  
	    });

	    // Add the price validation object
	    entry.addValidationLogic(priceValidation);

	    // Add the currency validation object
	    entry.addValidationLogic(currencyValidation);

	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 5 14 25-32 35-40 43 46"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "ui5/antares/ui/ValidationLogicCL", // Import the ValidationLogicCL class
	    "ui5/antares/types/ui/enums" // Import the enums
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, ValidationLogicCL, UIEnums) {
	      "use strict";

	      // Destructure the object to retrieve the ValidationOperator enum
	      const { ValidationOperator } = UIEnums;

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Create an object from the Validation Logic class for the price validation
	          const priceValidation = new ValidationLogicCL({
	            propertyName: "price", // price property of the Products
	            operator: ValidationOperator.BT,
	            value1: 1500,
	            value2: 2500,
	            message: "The price must be between 1500 and 2500",
	            invalidValueMessage: "Please only type number for the price field"      
	          });

	          // Create an object from the Validation Logic class for the currency validation
	          const currencyValidation = new ValidationLogicCL({
	            propertyName: "currency", // Currency property of the Products
	            operator: ValidationOperator.EQ,
	            value1: "EUR",
	            message: "Only EUR currency can be used"  
	          });

	          // Add the price validation object
	          entry.addValidationLogic(priceValidation);

	          // Add the currency validation object
	          entry.addValidationLogic(currencyValidation);

	          entry.createNewEntry();
	        }
	      });

	    });
	```

<table>
  <thead>
    <tr>
      <th>Validation Failed</th>
      <th>Invalid Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><img src="../images/create_entry/validation_logic_1.png?raw=true" alt="Validation Failed"></td>
      <td><img src="../images/create_entry/validation_logic_2.png?raw=true" alt="Invalid Value"></td>
    </tr>
  </tbody>
</table>

### Validation with Validator Function

[UI5_DATE_URL]: https://sapui5.hana.ondemand.com/#/api/module:sap/ui/core/date/UI5Date
[UI5_CONTROL_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.core.Control
[COMBOBOX_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.ComboBox

In the event that the validation logic is more complex than a simple check of specific values, it is possible to use a custom function for the purpose of validation.

It is essential that the function used for validation includes a parameter to retrieve the value entered by the end user. The UI5 Antares library transfers the user input or the [custom control](#custom-control) to the validator function as a parameter.

!!! info

	- The value passed to the validator function can be any one of the following types: string, number, boolean, JavaScript Date, [UI5 Date][UI5_DATE_URL], or [UI5 Control][UI5_CONTROL_URL].

	- If the validator function is utilized for the purpose of validating a [Custom Control](#custom-control), the type of the parameter will be the [UI5 Control][UI5_CONTROL_URL] that is added. To illustrate, if a [sap.m.ComboBox][COMBOBOX_URL] is added as a custom control, the [sap.m.ComboBox][COMBOBOX_URL] will be passed back to the validator function. 

	- Please note that for auto-generated form elements, the value can only be one of the following types: string, number, boolean, JavaScript Date, or [UI5 Date][UI5_DATE_URL].

The validator function must return a **boolean** value indicating whether the validation was successful or not.

<table>
  <thead>
    <tr>
      <th>Return Value</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>true</code></td>
      <td>Validation is successful</td>
    </tr>
    <tr>
      <td><code>false</code></td>
      <td>Validation is not successful</td>
    </tr>
  </tbody>
</table>

**Example**

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 4 18-23 26 31-39"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import ValidationLogicCL from "ui5/antares/ui/ValidationLogicCL"; // Import the ValidationLogicCL class
	import { ValidatorValueParameter } from "ui5/antares/types/ui/validation"; // Import the validator function parameter type

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // Create an object from the Validation Logic class for the currency validation
	    const currencyValidation = new ValidationLogicCL({
	      propertyName: "currency", // Currency property of the Products
	      validator: this.validateCurrency,
	      listener: this,
	      message: "Only EUR currency can be used"  
	    });

	    // Add the currency validation object
	    entry.addValidationLogic(currencyValidation);

	    entry.createNewEntry();
	  }

	  public validateCurrency(value: ValidatorValueParameter): boolean {
	    // Here you can write your own validation logic

	    if ((value as string) !== "EUR") {
	      return false; // Validation is not successful
	    }

	    return true; // Validation is successful
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 21-26 29 34-42"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "ui5/antares/ui/ValidationLogicCL" // Import the ValidationLogicCL class
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, ValidationLogicCL) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Create an object from the Validation Logic class for the currency validation
	          const currencyValidation = new ValidationLogicCL({
	            propertyName: "currency", // Currency property of the Products
	            validator: this._validateCurrency,
	            listener: this,
	            message: "Only EUR currency can be used"  
	          });

	          // Add the currency validation object
	          entry.addValidationLogic(currencyValidation);

	          entry.createNewEntry();
	        },

	        _validateCurrency: function (value) {
	          // Here you can write your own validation logic

	          if (value !== "EUR") {
	            return false; // Validation is not successful
	          }

	          return true; // Validation is successful
	        }
	      });

	    });
	```

### ValidationOperator Enum

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ValidationOperator.BT</td>
      <td>Between. Boundaries are included</td>
    </tr>
    <tr>
      <td>ValidationOperator.Contains</td>
      <td>Contains. It can only be used with <code>string</code> type</td>
    </tr>
    <tr>
      <td>ValidationOperator.EndsWith</td>
      <td>Ends with. It can only be used with <code>string</code> type</td>
    </tr>
    <tr>
      <td>ValidationOperator.EQ</td>
      <td>Equals</td>
    </tr>
    <tr>
      <td>ValidationOperator.GE</td>
      <td>Greater than or equals</td>
    </tr>
    <tr>
      <td>ValidationOperator.GT</td>
      <td>Greater than</td>
    </tr>
    <tr>
      <td>ValidationOperator.LE</td>
      <td>Less than or equals</td>
    </tr>
    <tr>
      <td>ValidationOperator.LT</td>
      <td>Less than</td>
    </tr>
    <tr>
      <td>ValidationOperator.NB</td>
      <td>Not between. Boundaries are included</td>
    </tr>
    <tr>
      <td>ValidationOperator.NE</td>
      <td>Not equals</td>
    </tr>
    <tr>
      <td>ValidationOperator.NotContains</td>
      <td>Not contains. It can only be used with <code>string</code> type</td>
    </tr>
    <tr>
      <td>ValidationOperator.NotEndsWith</td>
      <td>Not ends with. It can only be used with <code>string</code> type</td>
    </tr>
    <tr>
      <td>ValidationOperator.NotStartsWith</td>
      <td>Not starts with. It can only be used with <code>string</code> type</td>
    </tr>
    <tr>
      <td>ValidationOperator.StartsWith</td>
      <td>Starts with. It can only be used with <code>string</code> type</td>
    </tr>
  </tbody>
</table>

## Object Page

[ROUTER_INFO_URL]: https://sapui5.hana.ondemand.com/sdk/#/topic/e5200ee755f344c8aef8efcbab3308fb

The [createNewEntry()](#create-new-entry) method, by default, generates a [sap.m.Dialog][DIALOG_URL] including a [Simple Form][SIMPLEFORM_URL] or [Smart Form][SMARTFORM_URL] content, with configurations completed using the public methods. However, there may be instances where users require a larger screen, due to one or more of the reasons listed below:

- The `EntitySet` may have numerous properties, and displaying all of these in a dialog may not be user-friendly.
- The custom content may not fit in the dialog.

In such cases, the UI5 Antares library has the capability to generate a [sap.uxap.ObjectPageLayout][OBJECT_PAGE_URL] instead of a [sap.m.Dialog][DIALOG_URL], which may be a more suitable option.

!!! info

	All features of the dialog generation process are also available for use in the object page generation process.

The Entry classes default setting is to generate an object page with a single section containing all the properties of an `EntitySet`. The default title for this section is derived from the [Form Title](#form-title). If you wish to change the title of this section, please use the **setDefaultGroupTitle()** method.

!!! note

	Please note that any custom content added via the [addCustomContent()](#custom-content) or [addContentFromFragment()](#custom-content-from-fragment) methods will be added to a distinct section with a default title. The default title for the custom content is **Custom Contents**, but this can be modified using the **setCustomContentSectionTitle()** method.

To generate an object page for the `EntitySet`, the **setDisplayObjectPage()** method can be utilized. The second parameter of this method is the **target name** of the current view. This information is required by the library to redirect the end user back to the view where the object page was called. The target information can be obtained from the application's **manifest.json** file. To find the target name of the current page, please refer to the **"sap.ui5"."routing"."targets"** section in the **manifest.json** file.

!!! danger "Attention"

	- Please note that object page generation is only available when the SAPUI5 application has a [router][ROUTER_INFO_URL] initialized in the UI Component (Component.js). For your convenience, the UI5 Antares library uses the router of the application and does not initialize a new router instance.

	- The UI5 Antares library does not modify the hash when displaying the generated object page. Instead, a new target, designated as **UI5AntaresObjectPageTarget**, is created through the application's router. This target is then displayed in real-time.

!!! example

	Please refer to the example below, which illustrates the use of a target named **MyTarget** in the application's **manifest.json** file.

``` json title="manifest.json" linenums="1" hl_lines="18-24"
{
  "sap.app": {
    ...
  },
  "sap.ui": {
    ...
  },
  "sap.ui5": {
    ...
    "routing": {
      "config": {
        ...
      },
      "routes": [
        ...
      ],
      "targets": {
        "MyTarget": {
          "viewType": "XML",
          "transition": "slide",
          "clearControlAggregation": "true",
          "viewId": "MyViewId",
          "viewName": "MyViewName"
        },
        ...
      }
    }
  }
}
```

=== "Setter (setDisplayObjectPage)"

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
	      <td>display</td>
	      <td><code>boolean</code></td>
	      <td>Yes</td>
	      <td>If set to <strong>true</strong>, the library will generate an object page instead of a dialog</td>
	    </tr>
	    <tr>
	      <td>fromTarget</td>
	      <td><code>string</code></td>
	      <td>Yes</td>
	      <td>The target name of the view to where the end user should be redirected after finishing the process on the generated object page</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getDisplayObjectPage)"

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
	      <td>Returns all the values that were set using the <strong>setDisplayObjectPage()</strong> method. Default value is <strong>false</strong></td>
	    </tr>
	  </tbody>
	</table>

!!! example

	Let us consider an `EntitySet` named **Products**. Instead of using a dialog for the creation process, we would prefer to have an object page.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 17 20"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public onCreateProduct() {
	    // initialize
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // set the default section title
	    entry.setDefaultGroupTitle("My Default Section");

	    // activate the object page with the target
	    entry.setDisplayObjectPage(true, "MyCurrentViewTarget");

	    // call the object page
	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 21 24"
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
	          // initialize
	          const entry = new EntryCreateCL(this, "Products");

	          // set the default section title
	          entry.setDefaultGroupTitle("My Default Section");          

	          // activate the object page with the target
	          entry.setDisplayObjectPage(true, "MyCurrentViewTarget");

	          // call the object page
	          entry.createNewEntry(); 
	        }

	      });

	    });
	```

The generated object page with the default values will be similar in appearance to the following example.

![Object Page](./images/create_entry/object_page_1.png)

### Sections

To add sections to the generated object page, please utilize the [Form Grouping](#form-grouping) feature.

### Header Title

The title in the generated object page is derived from the [Form Title](#form-title) feature.

### Header Label

[AVATAR_URL]: https://sapui5.hana.ondemand.com/#/api/sap.m.Avatar

The label displayed on the right side of the [sap.m.Avatar][AVATAR_URL] can be modified using the **setObjectPageHeaderLabel()** method. Please find the default values for the header label below.

#### Default Values

<table>
  <thead>
    <tr>
      <th>Class</th>
      <th>Default Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="#entry-create">Entry Create</a></td>
      <td>You can create a new ${this.entityName} on this page.</td>
    </tr>
    <tr>
      <td><a href="../entry_update">Entry Update</a></td>
      <td>You can update ${this.entityName} on this page.</td>
    </tr>
    <tr>
      <td><a href="../entry-delete">Entry Delete</a></td>
      <td>You can delete ${this.entityName} on this page.</td>
    </tr>
    <tr>
      <td><a href="../entry-read">Entry Read</a></td>
      <td>You can display ${this.entityName} on this page.</td>
    </tr>
  </tbody>
</table>

=== "Setter (setObjectPageHeaderLabel)"

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
	      <td>label</td>
	      <td><code>string</code></td>
	      <td>Yes</td>
	      <td>The label which is displayed on the right side of the <a href="https://sapui5.hana.ondemand.com/#/api/sap.m.Avatar">sap.m.Avatar</a></td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getObjectPageHeaderLabel)"

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
	      <td>Returns all the values that were set using the <strong>setObjectPageHeaderLabel()</strong> method. Default value differs based on the Entry class</td>
	    </tr>
	  </tbody>
	</table>

**Example**

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 23"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public onCreateProduct() {
	    // initialize
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // set the header title
	    entry.setFormTitle("My Header Title");

	    // set the default section title
	    entry.setDefaultGroupTitle("My Default Section");

	    // set the header label
	    entry.setObjectPageHeaderLabel("My Header Label");

	    // activate the object page with the target
	    entry.setDisplayObjectPage(true, "MyCurrentViewTarget");

	    // call the object page
	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 27"
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
	          // initialize
	          const entry = new EntryCreateCL(this, "Products");

	          // set the header title
	          entry.setFormTitle("My Header Title");

	          // set the default section title
	          entry.setDefaultGroupTitle("My Default Section");

	          // set the header label
	          entry.setObjectPageHeaderLabel("My Header Label");

	          // activate the object page with the target
	          entry.setDisplayObjectPage(true, "MyCurrentViewTarget");

	          // call the object page
	          entry.createNewEntry();
	        }

	      });

	    });
	```

![Object Page](./images/create_entry/object_page_2.png)

### Header Avatar

To modify the src attribute of the [sap.m.Avatar][AVATAR_URL] displayed on the object page, the **setObjectPageAvatarSrc()** method can be utilized. The default values for the avatar are provided below for your reference.

#### Default Values

<table>
  <thead>
    <tr>
      <th>Class</th>
      <th>Default Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="#entry-create">Entry Create</a></td>
      <td>sap-icon://add</td>
    </tr>
    <tr>
      <td><a href="../entry-update">Entry Update</a></td>
      <td>sap-icon://edit</td>
    </tr>
    <tr>
      <td><a href="../entry-delete">Entry Delete</a></td>
      <td>sap-icon://delete</td>
    </tr>
    <tr>
      <td><a href="../entry-read">Entry Read</a></td>
      <td>sap-icon://display</td>
    </tr>
  </tbody>
</table>

=== "Setter (setObjectPageAvatarSrc)"

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
	      <td>src</td>
	      <td><code>string</code></td>
	      <td>Yes</td>
	      <td>The src attribute of the <a href="https://sapui5.hana.ondemand.com/#/api/sap.m.Avatar">sap.m.Avatar</a></td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getObjectPageAvatarSrc)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td>string</td>
	      <td>Returns all the values that were set using the <strong>setObjectPageAvatarSrc()</strong> method. Default value differs based on the Entry class</td>
	    </tr>
	  </tbody>
	</table>

**Example**

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 17"
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public onCreateProduct() {
	    // initialize
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // set the avatar src
	    entry.setObjectPageAvatarSrc("https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_%22G%22_logo.svg/1024px-Google_%22G%22_logo.svg.png");

	    // call the object page
	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 21"
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
	          // initialize
	          const entry = new EntryCreateCL(this, "Products");

	          // set the avatar src
	          entry.setObjectPageAvatarSrc("https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_%22G%22_logo.svg/1024px-Google_%22G%22_logo.svg.png");

	          // call the object page
	          entry.createNewEntry();
	        }

	      });

	    });
	```

![Object Page](./images/create_entry/object_page_3.png)

### Custom Content Section Title

To modify the default title for the custom content on the generated object page, the **setCustomContentSectionTitle()** method can be utilized.

=== "Setter (setCustomContentSectionTitle)"

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
	      <td>title</td>
	      <td><code>string</code></td>
	      <td>Yes</td>
	      <td>The title of the section that is generated for the custom contents</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getCustomContentSectionTitle)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td>string</td>
	      <td>Returns all the values that were set using the <strong>setCustomContentSectionTitle()</strong> method. Default value is <strong>Custom Contents</strong></td>
	    </tr>
	  </tbody>
	</table>

## Custom Control

By default, the UI5 Antares library creates [sap.ui.comp.smartfield.SmartField][SMARTFIELD_URL] when the form type is **SMART** or [sap.m.Input][101], [sap.m.Date The following components are generated: Picker, DateTimePicker, and CheckBox, depending on the Edm Type of the properties when the form type is Simple.

The UI5 Antares library generates the UI components shown below, depending on the [Form Type](#form-type).

1) **SIMPLE:** [sap.m.Input][INPUT_URL], [sap.m.DatePicker][DATEPICKER_URL], [sap.m.DateTimePicker][DATETIMEPICKER_URL], [sap.m.CheckBox][CHECKBOX_URL] depending on the `Edm.Type` of the `EntitySet` property.

2) **SMART:** [sap.ui.comp.smartfield.SmartField][SMARTFIELD_URL]

However, you may wish to consider implementing a different UI control for the properties of your `EntitySet`. In such cases, the **Custom Control** class allows the addition of a variety of user interface controls (such as the [sap.m.Slider](https://sapui5.hana.ondemand.com/#/api/sap.m.Slider)) for the properties of the `EntitySet`, as defined in the [constructor](#constructor).

!!! danger "Attention"

	Please note that a custom control can only be added for the properties of an `EntitySet` specified in the [constructor](#constructor).

During the form generation process, the UI5 Antares library initiates a search for **custom controls** to be utilized for the properties of the `EntitySet`. In the event that no custom control is identified, the library generates a UI control based on the [Form Type](#form-type) and `Edm.Type`.

### Constructor

In order to utilise the **CustomControlCL** class, it is necessary to initialise an object from that class.

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
      <td>control</td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.Control">Control</a></td>
      <td>Yes</td>
      <td></td>
      <td>The UI Control to add into the auto-generated form</td>
    </tr>
    <tr>
      <td>propertyName</td>
      <td><code>string</code></td>
      <td>Yes</td>
      <td></td>
      <td>This is the property of the <code>EntitySet</code> that is set in the <a href="#constructor">constructor</a> for which the custom control will be added.</td>
    </tr>
    <tr>
      <td>validator?</td>
      <td><a href="#validation-logic">ValidationLogicCL</a></td>
      <td>No</td>
      <td></td>
      <td>The validation object</td>
    </tr>
  </tbody>
</table>

!!! example

	Let us consider an `EntitySet` named **Products** with the following properties: `ID`, `name`, `description`, `price`, and `currency`. We wish to add a [sap.m.ComboBox][COMBOBOX_URL] with some predefined items for the `currency` property.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 4 5 19-26 29 32" 
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import CustomControlCL from "ui5/antares/ui/CustomControlCL"; // Import the Custom Control Class
	import ComboBox from "sap/m/ComboBox"; // Import the ComboBox
	import Item from "sap/ui/core/Item"; // Import the Item for ComboBox items

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // Create a custom control which is ComboBox in this sample
	    const currencyComboBox = new ComboBox({
	        selectedKey: "{currency}", // Do not forget to add the path of the property
	        items: [
	            new Item({ key: "EUR", text: "Euro" }),
	            new Item({ key: "USD", text: "US Dollar" }),
	            new Item({ key: "TRY", text: "Turkish Lira" }),
	        ]
	    });

	    // Create an object from the CustomControlCL class with the UI Control and property name
	    const currencyControl = new CustomControlCL(currencyComboBox, "currency");

	    // Add the custom control
	    entry.addCustomControl(currencyControl);

	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 5 6 23-30 33 36" 
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "ui5/antares/ui/CustomControlCL", // Import the Custom Control Class
	    "sap/m/ComboBox", // Import the ComboBox
	    "sap/ui/core/Item" // Import the Item for ComboBox items
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, CustomControlCL, ComboBox, Item) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Create a custom control which is ComboBox in this sample
	          const currencyComboBox = new ComboBox({
	              selectedKey: "{currency}", // Do not forget to add the path of the property
	              items: [
	                  new Item({ key: "EUR", text: "Euro" }),
	                  new Item({ key: "USD", text: "US Dollar" }),
	                  new Item({ key: "TRY", text: "Turkish Lira" }),
	              ]
	          });

	          // Create an object from the CustomControlCL class with the UI Control and property name
	          const currencyControl = new CustomControlCL(currencyComboBox, "currency");

	          // Add the custom control
	          entry.addCustomControl(currencyControl);

	          entry.createNewEntry();
	        }
	      });

	    });
	```

![Custom Control](./images/create_entry/custom_control_1.png)

### Validation

Furthermore, the custom controls can be configured to execute a [Validation Logic](#validation-logic) before the transient entity is submitted.

!!! warning

	As the addition of a custom UI control to the form is unpredictable, the library is unable to perform any validation or mandatory checks. Instead, this must be done **manually** using the [Validator Function](#validation-with-validator-function).

The UI5 Antares library passes the custom UI control, which has been added, as a parameter to the validator function.

!!! example

	Let us consider an `EntitySet` named **Products** with the following properties: `ID`, `name`, `description`, `price`, and `currency`. We wish to add a [sap.m.ComboBox][COMBOBOX_URL] with some predefined items for the `currency` property.

	It is also important to ensure that the end user does not leave the `currency` field blank and that they make a selection in this field.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 4 5 6 7 31-36 39 48-54" 
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import CustomControlCL from "ui5/antares/ui/CustomControlCL"; // Import the Custom Control Class
	import ValidationLogicCL from "ui5/antares/ui/ValidationLogicCL"; // Import the ValidationLogicCL class
	import { ValidatorValueParameter } from "ui5/antares/types/ui/validation"; // Import the validator function parameter type
	import ComboBox from "sap/m/ComboBox"; // Import the ComboBox
	import Item from "sap/ui/core/Item"; // Import the Item for ComboBox items

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // Create a custom control which is ComboBox in this sample
	    const currencyComboBox = new ComboBox({
	        selectedKey: "{currency}", // Do not forget to add the path of the property
	        items: [
	            new Item({ key: "EUR", text: "Euro" }),
	            new Item({ key: "USD", text: "US Dollar" }),
	            new Item({ key: "TRY", text: "Turkish Lira" }),
	        ]
	    });

	    // Create the validation object for the custom control
	    const currencyValidation = new ValidationLogicCL({
	      propertyName: "currency", // Currency property of the Products
	      validator: this.validateCurrency,
	      listener: this,
	      message: "The currency field is mandatory"        
	    });

	    // Create an object from the CustomControlCL class with the UI Control, property name and validation object
	    const currencyControl = new CustomControlCL(currencyComboBox, "currency", currencyValidation);

	    // Add the custom control
	    entry.addCustomControl(currencyControl);

	    entry.createNewEntry();
	  }

	  // UI5 Antares will pass the added combobox back to the validator function
	  public validateCurrency (control: ValidatorValueParameter): boolean {
	    if (!(control as ComboBox).getSelectedKey()) {
	      return false; // Validation is unsuccessful
	    }

	    return true; // Validation is successful
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 5 6 7 34-39 42 51-57" 
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "ui5/antares/ui/CustomControlCL", // Import the Custom Control Class
	    "ui5/antares/ui/ValidationLogicCL", // Import the ValidationLogicCL class
	    "sap/m/ComboBox", // Import the ComboBox
	    "sap/ui/core/Item" // Import the Item for ComboBox items
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, CustomControlCL, ValidationLogicCL, ComboBox, Item) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Create a custom control which is ComboBox in this sample
	          const currencyComboBox = new ComboBox({
	              selectedKey: "{currency}", // Do not forget to add the path of the property
	              items: [
	                  new Item({ key: "EUR", text: "Euro" }),
	                  new Item({ key: "USD", text: "US Dollar" }),
	                  new Item({ key: "TRY", text: "Turkish Lira" }),
	              ]
	          });

	          // Create the validation object for the custom control
	          const currencyValidation = new ValidationLogicCL({
	            propertyName: "currency", // Currency property of the Products
	            validator: this._validateCurrency,
	            listener: this,
	            message: "The currency field is mandatory"        
	          });

	          // Create an object from the CustomControlCL class with the UI Control, property name and validation object
	          const currencyControl = new CustomControlCL(currencyComboBox, "currency", currencyValidation);

	          // Add the custom control
	          entry.addCustomControl(currencyControl);

	          entry.createNewEntry();
	        },

	        // UI5 Antares will pass the added combobox back to the validator function
	        _validateCurrency: function (control) {
	          if (!control.getSelectedKey()) {
	            return false; // Validation is unsuccessful
	          }

	          return true; // Validation is successful          
	        }
	      });

	    });
	```

### Custom Control From Fragment

As an alternative, custom controls can be added to the auto-generated form by loading the UI controls from a custom fragment created in the application files.

!!! tip

	This approach allows for the addition of multiple controls simultaneously, eliminating the need to create UI controls in the controller. Custom controls can be organized in the `.fragment.xml` files

!!! danger "Attention"

	It is a requirement that a [custom data][CUSTOM_DATA_URL] with the **UI5AntaresEntityPropertyName** key be added to each UI control within the specified fragment. The value of the **UI5AntaresEntityPropertyName** key should be the **property name** of the `EntitySet` for which the custom control is added. Otherwise, the UI5 Antares library will be unable to identify for which property the UI control is being added.

!!! example

	Let us consider an `EntitySet` named **Products** with the following properties: `ID`, `name`, `description`, `price`, and `currency`. We wish to add a [sap.m.ComboBox][COMBOBOX_URL] with some predefined items for the `currency` property and a [sap.m.Slider](https://sapui5.hana.ondemand.com/#/api/sap.m.Slider) for the `price` property.

Firstly, a file with `.fragment.xml` extension should be created in the application files. The UI controls will be placed into this file.

``` xml title="CustomControls.fragment.xml" linenums="1" hl_lines="4 7 26" 
<core:FragmentDefinition
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
    xmlns:app="http://schemas.sap.com/sapui5/extension/sap.ui.core.CustomData/1"
>
    <ComboBox
        app:UI5AntaresEntityPropertyName="currency"
        selectedKey="{currency}"
    >
        <items>
            <core:Item
                key="EUR"
                text="Euro"
            />
            <core:Item
                key="USD"
                text="US Dollar"
            />
            <core:Item
                key="TRY"
                text="Turkish Lira"
            />
        </items>
    </ComboBox>
    <Slider
        app:UI5AntaresEntityPropertyName="price"
        width="100%"
        min="1000"
        max="100000"
        showAdvancedTooltip="true"
        showHandleTooltip="true"
        inputsAsTooltips="true"
        enableTickmarks="true"
        step="1000"
        class="sapUiMediumMarginBottom"
        value="{price}"
    />
</core:FragmentDefinition>
```

![Custom Control From Fragment](./images/create_entry/custom_control_fragment_1.png)

[FRAGMENT_CLASS_URL]: ./fragment_class.md

Secondly, an object from the [FragmentCL][FRAGMENT_CLASS_URL] should be instantiated with the controller and fragment path parameters.

!!! danger "Attention"

	Please be aware that the **addControlFromFragment()** method is **asynchronous** and must be awaited.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 17 20" 
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the Fragment class

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // Create an object from the FragmentCL class with the controller and fragment path parameters.
	    const fragment = new FragmentCL(this, "your.apps.namespace.path.to.FragmentFileName");

	    // Add the controls from the fragment. It is an asynchronous method and must be awaited.
	    await entry.addControlFromFragment(fragment);

	    entry.createNewEntry();
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 21 24" 
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "ui5/antares/ui/FragmentCL" // Import the Fragment class
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, FragmentCL) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Create an object from the FragmentCL class with the controller and fragment path parameters.
	          const fragment = new FragmentCL(this, "your.apps.namespace.path.to.FragmentFileName");

	          // Add the controls from the fragment. It is an asynchronous method and must be awaited.
	          await entry.addControlFromFragment(fragment);

	          entry.createNewEntry();
	        }
	      });

	    });
	```

![Custom Control From Fragment](./images/create_entry/custom_control_fragment_2.png)

#### Validation

Furthermore, the custom controls loaded from a fragment can be configured to execute a [Validation Logic](#validation-logic) before the transient entity is submitted.

!!! warning

	As the addition of a custom UI control to the form is unpredictable, the library is unable to perform any validation or mandatory checks. Instead, this must be done **manually** using the [Validator Function](#validation-with-validator-function).

The UI5 Antares library passes the custom UI control, which has been added, as a parameter to the validator function.

!!! danger "Attention"

	It is a requirement that a [custom data][CUSTOM_DATA_URL] with the **UI5AntaresValidationLogic** key be added to each UI control within the specified fragment. The value of the **UI5AntaresValidationLogic** key should be the **name** of the [validator function](#validation-with-validator-function) in the controller.

!!! tip

	The default message displayed by the end user when the validation is unsuccessful can be modified by setting a [custom data][CUSTOM_DATA_URL] with the key **UI5AntaresValidationMessage**. The value of the **UI5AntaresValidationMessage** can be either the message itself or the i18n binding.

!!! example

	Let us consider an `EntitySet` named **Products** with the following properties: `ID`, `name`, `description`, `price`, and `currency`. We wish to add a [sap.m.ComboBox][COMBOBOX_URL] with some predefined items for the `currency` property and a [sap.m.Slider](https://sapui5.hana.ondemand.com/#/api/sap.m.Slider) for the `price` property.

	We also would like to add validation and validation messages to the custom controls.

Firstly, a file with `.fragment.xml` extension should be created in the application files. The UI controls will be placed into this file.

``` xml title="CustomControls.fragment.xml" linenums="1" hl_lines="4 7 8 9 28 29 30" 
<core:FragmentDefinition
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
    xmlns:app="http://schemas.sap.com/sapui5/extension/sap.ui.core.CustomData/1"
>
    <ComboBox
        app:UI5AntaresEntityPropertyName="currency"
        app:UI5AntaresValidationLogic="onValidateCurrency"
        app:UI5AntaresValidationMessage="{i18n>currencyValidationFailed}"
        selectedKey="{currency}"
    >
        <items>
            <core:Item
                key="EUR"
                text="Euro"
            />
            <core:Item
                key="USD"
                text="US Dollar"
            />
            <core:Item
                key="TRY"
                text="Turkish Lira"
            />
        </items>
    </ComboBox>
    <Slider
        app:UI5AntaresEntityPropertyName="price"
        app:UI5AntaresValidationLogic="onValidatePrice"
        app:UI5AntaresValidationMessage="The price must be bigger than 15000"
        width="100%"
        min="1000"
        max="100000"
        showAdvancedTooltip="true"
        showHandleTooltip="true"
        inputsAsTooltips="true"
        enableTickmarks="true"
        step="1000"
        class="sapUiMediumMarginBottom"
        value="{price}"
    />
</core:FragmentDefinition>
```

![Custom Control From Fragment](./images/create_entry/custom_control_fragment_3.png)

Secondly, an object from the [FragmentCL][FRAGMENT_CLASS_URL] should be instantiated with the controller and fragment path parameters.

!!! danger "Attention"

	Please be aware that the **addControlFromFragment()** method is **asynchronous** and must be awaited.

=== "TypeScript"

	``` ts linenums="1" hl_lines="2 3 4 5 6 18 23 29-35 38-44" 
	import Controller from "sap/ui/core/mvc/Controller";
	import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
	import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the Fragment class
	import { ValidatorValueParameter } from "ui5/antares/types/ui/validation"; // Import the validator function parameter type
	import ComboBox from "sap/m/ComboBox";
	import Slider from "sap/m/Slider";

	/**
	 * @namespace your.apps.namespace
	 */
	export default class YourController extends Controller {
	  public onInit() {

	  }

	  public async onCreateProduct() {
	    const entry = new EntryCreateCL<IProducts>(this, "Products");

	    // Create an object from the FragmentCL class with the controller and fragment path parameters.
	    const fragment = new FragmentCL(this, "your.apps.namespace.path.to.FragmentFileName");

	    // Add the controls from the fragment. It is an asynchronous method and must be awaited.
	    await entry.addControlFromFragment(fragment);

	    entry.createNewEntry();
	  }

	  // The name of the validator function must match to the custom data UI5AntaresValidationLogic defined in the .fragment.xml file
	  public onValidateCurrency (control: ValidatorValueParameter): boolean {
	    if (!(control as ComboBox).getSelectedKey()) {
	      return false; // Validation is unsuccessful
	    }

	    return true; // Validation is successful
	  }

	  // The name of the validator function must match to the custom data UI5AntaresValidationLogic defined in the .fragment.xml file
	  public onValidatePrice (control: ValidatorValueParameter): boolean {
	    if ((control as Slider).getValue() <= 15000) {
	      return false; // Validation is unsuccessful
	    }

	    return true; // Validation is successful
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
	```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 21 24 30-36 39-45"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "ui5/antares/ui/FragmentCL" // Import the Fragment class
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, FragmentCL) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Create an object from the FragmentCL class with the controller and fragment path parameters.
	          const fragment = new FragmentCL(this, "your.apps.namespace.path.to.FragmentFileName");

	          // Add the controls from the fragment. It is an asynchronous method and must be awaited.
	          await entry.addControlFromFragment(fragment);

	          entry.createNewEntry();
	        },

	        // The name of the validator function must match to the custom data UI5AntaresValidationLogic defined in the .fragment.xml file
	        onValidateCurrency: function (control) {
	          if (!control.getSelectedKey()) {
	            return false; // Validation is unsuccessful
	          }

	          return true; // Validation is successful
	        },

	        // The name of the validator function must match to the custom data UI5AntaresValidationLogic defined in the .fragment.xml file
	        onValidatePrice: function (control) {
	          if (control.getValue() <= 15000) {
	            return false; // Validation is unsuccessful
	          }

	          return true; // Validation is successful
	        }        
	      });

	    });
	```

## Custom Content

It is possible to add any UI control to the dialog below the auto-generated form. 

!!! info

	It should be noted that there is a distinction between a [Custom Control](#custom-control) and **Custom Content**. A [Custom Control](#custom-control) can only be added for the properties of the `EntitySet` specified in the [constructor](#constructor), whereas a **custom content** is a UI control that is required on the dialog but is not included in the transient entity.

!!! danger "Attention"

	Please be advised that custom content is not included in the [Validation Logic](#validation-logic) process. The UI5 Antares is designed solely for the addition of custom content to the dialog. However, it is important to note that the custom content must be managed **manually**.

!!! example

	To illustrate, a custom content could be a [sap.m.Image](https://sapui5.hana.ondemand.com/#/api/sap.m.Image), a [sap.m.upload.UploadSet](https://sapui5.hana.ondemand.com/#/api/sap.m.upload.UploadSet), or any other UI control that is necessary for the functionality of the dialog.

To add a custom content to the dialog, the **addCustomContent()** method can be utilized.

=== "Setter (addCustomContent)"

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
	      <td>content</td>
	      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.Control">Control</a></td>
	      <td>Yes</td>
	      <td>The UI control to add into the dialog</td>
	    </tr>
	  </tbody>
	</table>

=== "Getter (getCustomContents)"

	<table>
	  <thead>
	    <tr>
	      <th>Returns</th>
	      <th>Description</th>
	    </tr>
	  </thead>
	  <tbody>
	    <tr>
	      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.Control">Control[]</a></td>
	      <td>Returns all the UI controls that were added using <strong>addCustomContent()</strong> method. Default value is <strong>[]</strong></td>
	    </tr>
	  </tbody>
	</table>

!!! example

	Let us consider an `EntitySet` named **Products** and we wish to add an [sap.m.upload.UploadSet](https://sapui5.hana.ondemand.com/#/api/sap.m.upload.UploadSet) to the dialog that the UI5 Antares will generate.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 17 18 21"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import UploadSet from "sap/m/upload/UploadSet"; // Import the Upload Set

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {
    
      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL(this, "Products");

        // Create the custom content
        const upload = new UploadSet();
        upload.addStyleClass("sapUiSmallMargin");

        // Add the custom content
        entry.addCustomContent(upload);

        entry.createNewEntry(); 
      }
    }
    ```

=== "JavaScript"

	``` js linenums="1" hl_lines="3 4 21 22 25"
	sap.ui.define([
	    "sap/ui/core/mvc/Controller",
	    "ui5/antares/entry/v2/EntryCreateCL", // Import the class
	    "sap/m/upload/UploadSet" // Import the Upload Set
	], 
	    /**
	     * @param {typeof sap.ui.core.mvc.Controller} Controller
	     */
	    function (Controller, EntryCreateCL, UploadSet) {
	      "use strict";

	      return Controller.extend("your.apps.namespace.YourController", {
	        onInit: function () {

	        },

	        onCreateProduct: async function () {
	          const entry = new EntryCreateCL(this, "Products");

	          // Create the custom content
	          const upload = new UploadSet();
	          upload.addStyleClass("sapUiSmallMargin");

	          // Add the custom content
	          entry.addCustomContent(upload);

	          entry.createNewEntry(); 
	        }
	      });

	    });
	```

![Custom Content](./images/create_entry/custom_content_1.png)

### Custom Content From Fragment

As an alternative method, you can also add custom content to the dialog by loading the UI controls from a custom fragment created in your application files.

!!! tip

	This approach allows for the addition of multiple contents simultaneously, eliminating the need to create UI controls in the controller. Custom contents can be organized in the `.fragment.xml` files

!!! example

    Let us consider an `EntitySet` named **Products** and we wish to add an [sap.m.Image](https://sapui5.hana.ondemand.com/#/api/sap.m.Image) and an [sap.m.RadioButtonGroup](https://sapui5.hana.ondemand.com/#/api/sap.m.RadioButtonGroup) loaded from a fragment to the dialog that UI5 Antares will generate.

Firstly, a file with `.fragment.xml` extension should be created in the application files. The UI controls will be placed into this file.

``` xml title="CustomContents.fragment.xml"
<core:FragmentDefinition
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <VBox>
        <FlexBox justifyContent="Center">
            <Image
                class="sapUiSmallMargin"
                src="./img/antares.jpg"
                width="10rem"
            />
        </FlexBox>
        <RadioButtonGroup selectedIndex="0">
            <buttons>
                <RadioButton text="Option 1" />
                <RadioButton text="Option 2" />
                <RadioButton text="Option 3" />
            </buttons>
        </RadioButtonGroup>
    </VBox>
</core:FragmentDefinition>
```

![Custom Content From Fragment](./images/create_entry/custom_content_fragment_1.png)

Secondly, an object from the [FragmentCL][FRAGMENT_CLASS_URL] should be instantiated with the controller and fragment path parameters.

!!! danger "Attention"

	Please be aware that the **addContentFromFragment()** method is **asynchronous** and must be awaited.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 17 20"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the Fragment class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        const entry = new EntryCreateCL<IProducts>(this, "Products");

        // Create an object from the FragmentCL class with the controller and fragment path parameters.
        const fragment = new FragmentCL(this, "your.apps.namespace.path.to.FragmentFileName");

        // Add the controls from the fragment. It is an asynchronous method and must be awaited.
        await entry.addContentFromFragment(fragment);

        entry.createNewEntry();
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
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 4 21 24"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryCreateCL", // Import the class
        "ui5/antares/ui/FragmentCL" // Import the Fragment class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryCreateCL, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              const entry = new EntryCreateCL(this, "Products");

              // Create an object from the FragmentCL class with the controller and fragment path parameters.
              const fragment = new FragmentCL(this, "your.apps.namespace.path.to.FragmentFileName");

              // Add the controls from the fragment. It is an asynchronous method and must be awaited.
              await entry.addContentFromFragment(fragment);

              entry.createNewEntry();
            }
          });

        });
    ```

![Custom Content From Fragment](./images/create_entry/custom_content_fragment_2.png)

## Custom Fragment

If you do not wish to use the library-generated form and content, an alternative option is to use your own fragment and dialog with any desired content. The UI5 Antares library will create a transient entity and open the dialog loaded from your fragment. Additionally, the transient entity will be bound to the dialog.

!!! danger "Attention"

    Please note that certain features of the UI5 Antares library are only accessible when the dialog generation is handled by the library itself. If you wish to utilise a custom fragment, you will be required to perform certain steps manually.

1) The [Entry Create](#entry-create) and [Entry Update][ENTRY_UPDATE_URL] classes provide a public method named **submit()**, which must be called **manually** in order to submit the transient entity.

2) In the event that a transient entity should not be submitted and the dialog should be closed and destroyed, the **reset()** method, which is provided by the Entry classes, must be called.

!!! tip

    The **submit(resetAllOnFail:boolean = false)** method accepts an optional parameter with a default value of false. This parameter indicates whether all transient entities in the OData V2 Model should be reset or only the current context. If you wish to **reset all** transient entities, please set this parameter to **true**. If you only wish to reset the current transient entity, there is no need to set this parameter. 

!!! tip

    Please be advised that the fragment to be loaded must contain a [sap.m.Dialog][DIALOG_URL] and that all UI controls must be placed as the content of the dialog.

!!! danger "Attention"

    It should be noted that only a limited number of features of the UI5 Antares library can be utilized with a custom fragment.

### Supported Features

<table>
  <thead>
    <tr>
      <th>Feature</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="#mandatory-properties">Mandatory Properties</a></td>
      <td>By default, UI5 Antares performs a mandatory check for the properties with <strong>Nullable="false"</strong> attribute and the properties defined using <strong>setMandatoryProperties()</strong> method. However, this feature can be deactived using <a href="#auto-mandatory-check">setAutoMandatoryCheck()</a> method</td>
    </tr>
    <tr>
      <td><a href="#mandatory-error-message">Mandatory Error Message</a></td>
      <td>The default message that is displayed when the mandatory check is unsuccessful can be modified</td>
    </tr>
    <tr>
      <td><a href="#attach-submit-completed">Attach Submit Completed</a></td>
      <td>It is possible to create a function that will be called after the transient entity has been submitted successfully</td>
    </tr>
    <tr>
      <td><a href="#attach-submit-failed">Attach Submit Failed</a></td>
      <td>It is possible to create a function that will be called if the transient entity submission fails</td>
    </tr>
    <tr>
      <td><a href="#properties-with-edmguid-type">Properties with Edm.Guid Type</a></td>
      <td>By default, UI5 Antares generates random UUID for the <strong>key</strong> properties with Edm.Guid type</td>
    </tr>
  </tbody>
</table>

To use a custom fragment, the **setFragmentPath()** method can be utilized.

=== "Setter (setFragmentPath)"

    <table>
      <tr>
        <th>Parameter</th>
        <th>Type</th>
        <th>Mandatory</th>
        <th>Description</th>
      </tr>
      <tr>
        <td>fragmentPath</td>
        <td><code>string</code></td>
        <td>Yes</td>
        <td>The path of the fragment that will be loaded by the library</td>
      </tr>
      <tr>
        <td>containsSmartForm?</td>
        <td><code>boolean</code></td>
        <td>No</td>
        <td>It should be set to <strong>true</strong> if the dialog contains a <a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.comp.smartform.SmartForm">Smart Form</a></td>
      </tr>
    </table>

=== "Getter (getFragmentPath)"

    <table>
      <tr>
        <th>Returns</th>
        <th>Description</th>
      </tr>
      <tr>
        <td><code>string</code> | <code>undefined</code></td>
        <td>Returns the fragment path that was set using <strong>getFragmentPath()</strong> method. Default value is <strong>undefined</strong></td>
      </tr>
    </table>

!!! example

    Let us assume we are working with an `EntitySet` called **Products**. Rather than having the library generate a form, our goal is to create a custom fragment.

Firstly, a file with `.fragment.xml` extension should be created in the application files. The UI controls will be placed into this file.

``` xml title="MCustomDialog.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Dialog title="Create New Product">
        <form:SimpleForm>
            <form:content>
                <Label text="Product ID" />
                <MaskInput
                    mask="CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCCC"
                    value="{ID}"
                    placeholderSymbol="_"
                >
                    <rules>
                        <MaskInputRule
                            maskFormatSymbol="C"
                            regex="[a-f0-9]"
                        />
                    </rules>
                </MaskInput>
                <Label text="Name" />
                <Input value="{name}" />
                <Label text="Description" />
                <TextArea
                    value="{description}"
                    rows="5"
                />
                <Label text="Price" />
                <Slider
                    width="100%"
                    min="1000"
                    max="100000"
                    showAdvancedTooltip="true"
                    showHandleTooltip="true"
                    inputsAsTooltips="true"
                    enableTickmarks="true"
                    step="1000"
                    class="sapUiMediumMarginBottom"
                    value="{price}"
                />
                <Label text="Currency" />
                <ComboBox selectedKey="{currency}">
                    <items>
                        <core:Item
                            key="EUR"
                            text="Euro"
                        />
                        <core:Item
                            key="USD"
                            text="US Dollar"
                        />
                        <core:Item
                            key="TRY"
                            text="Turkish Lira"
                        />
                    </items>
                </ComboBox>
            </form:content>
        </form:SimpleForm>
        <beginButton>
            <Button
                text="Complete"
                press="onCompleteProduct"
            />
        </beginButton>
        <endButton>
            <Button
                text="Close"
                press="onCloseProductDialog"
            />
        </endButton>
    </Dialog>
</core:FragmentDefinition>
```

![Custom Fragment](./images/create_entry/custom_fragment_1.png)

Secondly, an object from the [Entry Create](#entry-create) class should be instantiated with the controller and `EntitySet` name.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 4 12 20 23 26 29 32 36-41 44-47 50-61 64-75"
    import Controller from "sap/ui/core/mvc/Controller";
    import EntryCreateCL from "ui5/antares/entry/v2/EntryCreateCL"; // Import the class
    import ResponseCL from "ui5/antares/entry/v2/ResponseCL"; // Import the ResponseCL class
    import { ISubmitResponse } from "ui5/antares/types/entry/submit"; // Import the error type
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      // Class property
      private productEntry: EntryCreateCL<IProducts>;

      public onInit() {

      }

      public async onCreateProduct () {
        // Create an object and set it to the class property
        this.productEntry = new EntryCreateCL<IProducts>(this, "Products");

        // Set the path of the custom fragment
        this.productEntry.setFragmentPath("your.apps.namespace.path.to.FragmentFileName");

        // Attach submit completed
        this.productEntry.attachSubmitCompleted(this.productSubmitCompleted, this);

        // Attach submit failed
        this.productEntry.attachSubmitFailed(this.productSubmitFailed, this);

        // Load the fragment
        this.productEntry.createNewEntry();
      }

      // Press event of the begin button in the dialog
      public onCompleteProduct () {
        // Do your validation

        // Submit the entity
        this.productEntry.submit();
      }

      // Press event of the end button in the dialog
      public onCloseProductDialog () {
        // Reset the entity and close the dialog
        this.productEntry.reset();
      } 

      // Submit Completed Handler
      private productSubmitCompleted (response: ResponseCL<IProducts>): void {
        // Get the status code. Please be aware, it may also be undefined
        const statusCode = response.getStatusCode();

        // Get the data that was submitted. Please be aware, it may also be undefined
        const submittedData = response.getResponse();

        if (submittedData) {
          // Some operations
          const createdProductID = submittedData.ID;
        }
      }

      // Please use the ISubmitResponse type for the ResponseCL generic
      private productSubmitFailed(response: ResponseCL<ISubmitResponse>): void {
        // Get the status code. Please be aware, it may also be undefined
        const statusCode = response.getStatusCode();

        // Get the response. Please be aware, it may also be undefined
        const reason = response.getResponse();

        // Get the statusText
        if (reason) {
          MessageBox.error(reason.statusText || "The product was not created!");
        }
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
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 19 22 25 28 31 35-40 43-46 49-60 63-74"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/entry/v2/EntryCreateCL", // Import the class
        "sap/m/MessageBox" // Import the Fragment class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, EntryCreateCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // Create an object and set it to the class property
              this.productEntry = new EntryCreateCL(this, "Products");

              // Set the path of the custom fragment
              this.productEntry.setFragmentPath("your.apps.namespace.path.to.FragmentFileName");

              // Attach submit completed
              this.productEntry.attachSubmitCompleted(this.productSubmitCompleted, this);

              // Attach submit failed
              this.productEntry.attachSubmitFailed(this.productSubmitFailed, this);

              // Load the fragment
              this.productEntry.createNewEntry();
            },

            // Press event of the begin button in the dialog
            onCompleteProduct: function () {
              // Do your validation

              // Submit the entity
              this.productEntry.submit();
            },

            // Press event of the end button in the dialog
            onCloseProductDialog: function () {
              // Reset the entity and close the dialog
              this.productEntry.reset();
            }, 

            // Submit Completed Handler
            productSubmitCompleted: function (response) {
              // Get the status code. Please be aware, it may also be undefined
              const statusCode = response.getStatusCode();

              // Get the data that was submitted. Please be aware, it may also be undefined
              const submittedData = response.getResponse();

              if (submittedData) {
                // Some operations
                const createdProductID = submittedData.ID;
              }
            },

            // Submit Failed Handler
            productSubmitFailed: function (response) {
              // Get the status code. Please be aware, it may also be undefined
              const statusCode = response.getStatusCode();

              // Get the response. Please be aware, it may also be undefined
              const reason = response.getResponse();

              // Get the statusText
              if (reason) {
                MessageBox.error(reason.statusText || "The product was not created!");
              }
            }   
          });

        });
    ```

![Custom Fragment](./images/create_entry/custom_fragment_2.png)

### Auto Mandatory Check

The UI5 Antares library performs a mandatory check for properties with the **Nullable="false"** attribute and those defined using the [setMandatoryProperties()](#mandatory-properties) method by default. However, this feature can be disabled using the **setAutoMandatoryCheck()** method if required.

=== "Setter (setAutoMandatoryCheck)"

    <table>
      <tr>
        <th>Parameter</th>
        <th>Type</th>
        <th>Mandatory</th>
        <th>Description</th>
      </tr>
      <tr>
        <td>autoMandatoryCheck</td>
        <td><code>boolean</code></td>
        <td>Yes</td>
        <td>If set to <strong>false</strong>, mandatory check is deactivated</td>
      </tr>
    </table>

=== "Getter (getAutoMandatoryCheck)"

    <table>
      <tr>
        <th>Returns</th>
        <th>Description</th>
      </tr>
      <tr>
        <td><code>boolean</code></td>
        <td>Returns the value that was set using <strong>setAutoMandatoryCheck()</strong> method. Default value is <strong>true</strong></td>
      </tr>
    </table>