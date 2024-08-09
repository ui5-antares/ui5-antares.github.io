# OData Create

[ODATA_MODEL_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.model.odata.v2.ODataModel

The ODataCreateCL class leverages the [sap.ui.model.odata.v2.ODataModel][ODATA_MODEL_URL] to facilitate the handling of POST (CREATE) requests in a promisified manner.

## Constructor

In order to utilise the functionality of ODataCreateCL, it is necessary to initialise the object.

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

    The **ODataCreateCL&lt;EntityT&gt;** is a generic class that can be initialized with a type containing the properties of the `EntitySet` specified as a parameter in the [class constructor](#constructor).

    - The `EntityT` type is used as the type for the `data` parameter of the **setData(data: EntityT)** method and as the return type for the **getData(): EntityT** method.
    - Additionally, the `EntityT` type is utilized as the return type for the **create(): Promise&lt;EntityT&gt;** method, which is employed to facilitate the POST request through the [sap.ui.model.odata.v2.ODataModel][ODATA_MODEL_URL].

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 14 19 24"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataCreateCL from "ui5/antares/odata/v2/ODataCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        // Initialize without a type
        const odata = new ODataCreateCL(this, "Products"); 
      }

      public async onCreateCategory() {
        // Initialize with a type
        const odata = new ODataCreateCL<ICategory>(this, "Categories"); 
      }

      public async onCreateCustomer() {
        // Initialize with a model name
        const odata = new ODataCreateCL(this, "Customers", "myODataModelName"); 
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
        "ui5/antares/odata/v2/ODataCreateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataCreateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // Initialize
              const odata = new ODataCreateCL(this, "Products"); 
            },

            onCreateCategory: async function () {
              // Initialize with a model name
              const odata = new ODataCreateCL(this, "Categories", "myODataModelName");
            }
          });

        });
    ```

## Set Data

To define the data that will be transmitted via the POST HTTP request body, the **setData()** method can be utilized.

!!! tip 

    It is also possible to set the data for the navigation properties (deep create).

=== "Setter (setData)"

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
          <td>data</td>
          <td><code>EntityT</code></td>
          <td>Yes</td>
          <td>The data that will be sent via the POST HTTP request body</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getData)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>EntityT</code></td>
          <td>Returns the value that was set using <strong>setData()</strong> method. Default value is <strong>undefined</strong></td>
        </tr>
      </tbody>
    </table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 17-27"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataCreateCL from "ui5/antares/odata/v2/ODataCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        // Initialize with a type
        const odata = new ODataCreateCL<IProducts>(this, "Products"); 

        // set the http body
        odata.setData({
          ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18",
          name: "Product Name",
          description: "Description",
          brand: "Brand",
          price: 999,
          currency: "EUR",
          quantityInStock: 15,
          categoryID: "eb7783a6-f30f-4f7d-b85c-f211eea54568",
          supplierID: "7734d25f-0d1e-4ab4-b8a8-cd201f29fd2f"
        });
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

    ``` js linenums="1" hl_lines="3 21-31"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataCreateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataCreateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // Initialize
              const odata = new ODataCreateCL(this, "Products"); 

              // set the http body
              odata.setData({
                ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18",
                name: "Product Name",
                description: "Description",
                brand: "Brand",
                price: 999,
                currency: "EUR",
                quantityInStock: 15,
                categoryID: "eb7783a6-f30f-4f7d-b85c-f211eea54568",
                supplierID: "7734d25f-0d1e-4ab4-b8a8-cd201f29fd2f"
              });
            }
          });

        });
    ```

## Create Request

To send a POST (Create) request through the OData V2 model, you can use the **create()** method.

!!! danger "Attention"

    It is a prerequisite that data be set using the [setData()](#set-data) method before calling the **create()** method.

!!! info

    - The **create()** method runs **asynchronously** and can be awaited. 
    - If the request is successful, the **create()** method will return the data of the newly created entity.

!!! warning

    In the event of a failed POST request, the [OData Create](#odata-create) class will generate an error message. To ensure the error is identified and addressed, it is recommended to call the **create()** method within a **try-catch** block.

### Error Type

In the event of a failed POST request, the object generated by the class can contain the properties outlined below.

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
      <td>&nbsp;&nbsp;&nbsp;&nbsp;headers?: <code>object</code> | <code>undefined</code></td>
      <td>The HTTP response headers.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;message?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP response message.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;responseText?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP response text.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusCode?: <code>string</code> | <code>number</code> | <code>undefined</code></td>
      <td>The status code of the HTTP request.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusText?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP status text.</td>
    </tr>
  </tbody>
</table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 19-29 31-38"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataCreateCL from "ui5/antares/odata/v2/ODataCreateCL"; // Import the class
    import { IError } from "ui5/antares/types/common"; // Import the error type
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        // Initialize with a type
        const odata = new ODataCreateCL<IProducts>(this, "Products"); 

        // set the http body
        odata.setData({
          ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18",
          name: "Product Name",
          description: "Description",
          brand: "Brand",
          price: 999,
          currency: "EUR",
          quantityInStock: 15,
          categoryID: "eb7783a6-f30f-4f7d-b85c-f211eea54568",
          supplierID: "7734d25f-0d1e-4ab4-b8a8-cd201f29fd2f"
        });

        try {
          // send the http request and get the result
          const result = await odata.create();
          MessageBox.information(result.ID + " was created.");
        } catch (error) {
          // catch the error
          MessageBox.error((error as IError).message || "Request failed");
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

    ``` js linenums="1" hl_lines="3 22-32 34-41"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataCreateCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataCreateCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // Initialize
              const odata = new ODataCreateCL(this, "Products"); 

              // set the http body
              odata.setData({
                ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18",
                name: "Product Name",
                description: "Description",
                brand: "Brand",
                price: 999,
                currency: "EUR",
                quantityInStock: 15,
                categoryID: "eb7783a6-f30f-4f7d-b85c-f211eea54568",
                supplierID: "7734d25f-0d1e-4ab4-b8a8-cd201f29fd2f"
              });

              try {
                // send the http request and get the result
                const result = await odata.create();
                MessageBox.information(result.ID + " was created.");
              } catch (error) {
                // catch the error
                MessageBox.error(error.message || "Request failed");
              }          
            }
          });

        });
    ```

## URL Parameters

Prior to sending the POST request with the [create()](#create-request) method, it is possible to set the URL parameters using the **setUrlParameters()** method.

=== "Setter (setUrlParameters)"

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
          <td>urlParameters</td>
          <td><code>Record&lt;string, string&gt;</code></td>
          <td>Yes</td>
          <td>The URL parameters of the POST request</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getUrlParameters)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>Record&lt;string, string&gt; | undefined</code></td>
          <td>Returns the value that was set using <strong>setUrlParameters()</strong> method. Default value is <strong>undefined</strong></td>
        </tr>
      </tbody>
    </table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 17-19"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataCreateCL from "ui5/antares/odata/v2/ODataCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        // Initialize with a type
        const odata = new ODataCreateCL<IProducts>(this, "Products"); 

        // set the url parameters
        odata.setUrlParameters({
          "$expand": "toProductLocations"
        });
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

    ``` js linenums="1" hl_lines="3 21-23"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataCreateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataCreateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // Initialize
              const odata = new ODataCreateCL(this, "Products"); 

              // set the url parameters
              odata.setUrlParameters({
                "$expand": "toProductLocations"
              });         
            }
          });

        });
    ```

## Refresh After Change

The OData V2 model will be automatically refreshed after the POST request has been completed.

To change the default behavior, the **setRefreshAfterChange()** method can be utilized.

=== "Setter (setRefreshAfterChange)"

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
          <td>refreshAfterChange</td>
          <td><code>boolean</code></td>
          <td>Yes</td>
          <td>If set to <strong>false</strong>, the OData V2 model will not be refreshed after the request has been completed</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getRefreshAfterChange)"

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
          <td>Returns the value that was set using <strong>setRefreshAfterChange()</strong> method. Default value is <strong>true</strong></td>
        </tr>
      </tbody>
    </table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 17"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataCreateCL from "ui5/antares/odata/v2/ODataCreateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        // Initialize with a type
        const odata = new ODataCreateCL<IProducts>(this, "Products"); 

        // deactivate the auto model refresh
        odata.setRefreshAfterChange(false);
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
        "ui5/antares/odata/v2/ODataCreateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataCreateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // Initialize
              const odata = new ODataCreateCL(this, "Products"); 

              // deactivate the auto model refresh
              odata.setRefreshAfterChange(false);      
            }
          });

        });
    ```

## Additional Response Info

The [create()](#create-request) method returns the data of the successfully created entity. However, you may require further information such as the `status code` and `headers`.

Once the [create()](#create-request) function has been completed, the **getResponse()** method can be utilized to obtain further details.

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
      <td>&nbsp;&nbsp;&nbsp;&nbsp;$reported?: <code>boolean</code> | <code>undefined</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;body?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP body</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;headers?: <code>object</code> | <code>undefined</code></td>
      <td>The HTTP response headers.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusCode?: <code>string</code> | <code>number</code> | <code>undefined</code></td>
      <td>The status code of the HTTP request.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusText?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP status text.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;_imported?: <code>boolean</code> | <code>undefined</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;data?: <code>EntityT</code> | <code>undefined</code></td>
      <td>The data that was created</td>
    </tr>
  </tbody>
</table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 37 39-41"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataCreateCL from "ui5/antares/odata/v2/ODataCreateCL"; // Import the class
    import { IError } from "ui5/antares/types/common"; // Import the error type
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onCreateProduct() {
        // Initialize with a type
        const odata = new ODataCreateCL<IProducts>(this, "Products"); 

        // set the http body
        odata.setData({
          ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18",
          name: "Product Name",
          description: "Description",
          brand: "Brand",
          price: 999,
          currency: "EUR",
          quantityInStock: 15,
          categoryID: "eb7783a6-f30f-4f7d-b85c-f211eea54568",
          supplierID: "7734d25f-0d1e-4ab4-b8a8-cd201f29fd2f"
        });

        try {
          // send the http request and get the result
          const result = await odata.create();
          MessageBox.information(result.ID + " was created.");

          // get the additional response info
          const response = odata.getResponse();

          if (response) {
            console.log("Status Code: " + response.statusCode);
          }
        } catch (error) {
          // catch the error
          MessageBox.error((error as IError).message || "Request failed");
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

    ``` js linenums="1" hl_lines="3 40 42-44"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataCreateCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataCreateCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onCreateProduct: async function () {
              // Initialize
              const odata = new ODataCreateCL(this, "Products"); 

              // set the http body
              odata.setData({
                ID: "b2f0013e-418f-42aa-9a24-3770fe17ce18",
                name: "Product Name",
                description: "Description",
                brand: "Brand",
                price: 999,
                currency: "EUR",
                quantityInStock: 15,
                categoryID: "eb7783a6-f30f-4f7d-b85c-f211eea54568",
                supplierID: "7734d25f-0d1e-4ab4-b8a8-cd201f29fd2f"
              });

              try {
                // send the http request and get the result
                const result = await odata.create();
                MessageBox.information(result.ID + " was created.");

                // get the additional response info
                const response = odata.getResponse();

                if (response) {
                  console.log("Status Code: " + response.statusCode);
                }            
              } catch (error) {
                // catch the error
                MessageBox.error(error.message || "Request failed");
              }          
            }
          });

        });
    ```

## Create Entry

To create a transient entity context which can be bound to a form or a dialog, the **createEntry()** method can be utilized.

!!! tip

    If you wish to create the [context](https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Context) with the initial values, the [setData()](#set-data) method can be utilized.

<table>
  <thead>
    <tr>
      <th>Returns</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Context">Context</a></code></td>
      <td>The context of the transient entity</td>
    </tr>
  </tbody>
</table>