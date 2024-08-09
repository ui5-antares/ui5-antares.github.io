# OData Update

[ODATA_MODEL_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.model.odata.v2.ODataModel

The ODataUpdateCL class leverages the [sap.ui.model.odata.v2.ODataModel][ODATA_MODEL_URL] to facilitate the handling of PATCH/MERGE/PUT (UPDATE) requests in a promisified manner.

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

    The **ODataUpdateCL&lt;EntityT, EntityKeysT&gt;** is a generic class and can be initialized with 2 types.

    - The `EntityT` type contains **all** properties of the `EntitySet` that is specified on the class constructor.
    - The `EntityKeysT` type contains the **key** properties of the `EntitySet` that is specified on the class constructor.

    The `EntityT` type is used as the type for the `data` parameter of the **setData(data: EntityT)** method and as the return type for the **getData(): EntityT** and **update(): Promise&lt;EntityT&gt;** methods.

    The `EntityKeysT` type is used as the type for the `keys` parameter of the **update(keys: EntityKeysT)** method.

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 14 19 24"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataUpdateCL from "ui5/antares/odata/v2/ODataUpdateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onUpdateProduct() {
        // Initialize without a type
        const odata = new ODataUpdateCL(this, "Products"); 
      }

      public async onUpdateCategory() {
        // Initialize with a type
        const odata = new ODataUpdateCL<ICategory, ICategoryKeys>(this, "Categories"); 
      }

      public async onUpdateCustomer() {
        // Initialize with a model name
        const odata = new ODataUpdateCL(this, "Customers", "myODataModelName"); 
      }
    }

    // The properties that will be updated should not be optional
    interface ICategory {
      ID: string;
      name: string;
    }

    interface ICategoryKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18 23"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataUpdateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataUpdateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onUpdateProduct: async function () {
              // Initialize
              const odata = new ODataUpdateCL(this, "Products"); 
            },

            onUpdateCategory: async function () {
              // Initialize with a model name
              const odata = new ODataUpdateCL(this, "Categories", "myODataModelName");
            }
          });

        });
    ```

## Set Data

To define the data that will be transmitted via the UPDATE HTTP request body, the **setData()** method can be utilized.

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
          <td>The data that will be sent via the UPDATE HTTP request body</td>
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

    ``` ts linenums="1" hl_lines="2 17-20"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataUpdateCL from "ui5/antares/odata/v2/ODataUpdateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onUpdateProduct() {
        // Initialize with a type
        const odata = new ODataUpdateCL<IProducts, IProductKeys>(this, "Products"); 

        // set the http body to update
        odata.setData({
          name: "New Product Name",
          description: "New Description"
        });
      }

    }

    // The properties that will be updated should not be optional
    interface IProducts {
      ID?: string;
      name: string;
      description: string;
      brand?: string;
      price?: number;
      currency?: string;
      quantityInStock?: number;
      categoryID?: string;
      supplierID?: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21-24"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataUpdateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataUpdateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onUpdateProduct: async function () {
              // Initialize
              const odata = new ODataUpdateCL(this, "Products"); 

              // set the http body to update
              odata.setData({
                name: "New Product Name",
                description: "New Description"
              });
            }
          });

        });
    ```

## Update Request

To send a PATCH/MERGE/PUT (UPDATE) request through the OData V2 model, you can use the **update(keys: EntityKeysT)** method.

!!! danger "Attention"

    It is a prerequisite that data be set using the [setData()](#set-data) method before calling the **update()** method.

!!! info

    - The **update()** method runs **asynchronously** and can be awaited. 
    - If the request is successful, the **update()** method will return the data of the updated entity.

!!! warning

    In the event of a failed UPDATE request, the [OData Update](#odata-update) class will generate an error message. To ensure the error is identified and addressed, it is recommended to call the **update()** method within a **try-catch** block.

### Error Type

In the event of a failed UPDATE request, the object generated by the class can contain the properties outlined below.

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

    ``` ts linenums="1" hl_lines="2 3 19-22 24-34"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataUpdateCL from "ui5/antares/odata/v2/ODataUpdateCL"; // Import the class
    import { IError } from "ui5/antares/types/common"; // Import the error type
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onUpdateProduct() {
        // Initialize with a type
        const odata = new ODataUpdateCL<IProducts, IProductKeys>(this, "Products"); 

        // set the http body to update
        odata.setData({
          name: "New Product Name",
          description: "New Description"
        });

        try {
          // send the http request and get the result. Note: You have to specify the key values of the entity that will be updated
          const result = await odata.update({
            ID: "3ccb5dd2-cc12-483a-b569-a6ec844f8f0b"
          });

          MessageBox.information("The entity with the following ID: " + result.ID + " was updated.");
        } catch (error) {
          // catch the error
          MessageBox.error((error as IError).message || "Request failed");
        }
      }

    }

    // The properties that will be updated should not be optional
    interface IProducts {
      ID?: string;
      name: string;
      description: string;
      brand?: string;
      price?: number;
      currency?: string;
      quantityInStock?: number;
      categoryID?: string;
      supplierID?: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 22-25 27-37"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataUpdateCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataUpdateCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onUpdateProduct: async function () {
              // Initialize
              const odata = new ODataUpdateCL(this, "Products"); 

              // set the http body to update
              odata.setData({
                name: "New Product Name",
                description: "New Description"
              });

              try {
                // send the http request and get the result. Note: You have to specify the key values of the entity that will be updated
                const result = await odata.update({
                  ID: "3ccb5dd2-cc12-483a-b569-a6ec844f8f0b"
                });

                MessageBox.information("The entity with the following ID: " + result.ID + " was updated.");
              } catch (error) {
                // catch the error
                MessageBox.error(error.message || "Request failed");
              }          
            }
          });

        });
    ```

## URL Parameters

Prior to sending the UPDATE request with the [update()](#update-request) method, it is possible to set the URL parameters using the **setUrlParameters()** method.

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
          <td>The URL parameters of the UPDATE request</td>
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
    import ODataUpdateCL from "ui5/antares/odata/v2/ODataUpdateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onUpdateProduct() {
        // Initialize with a type
        const odata = new ODataUpdateCL<IProducts, IProductKeys>(this, "Products"); 

        // set the url parameters
        odata.setUrlParameters({
          "$expand": "toProductLocations"
        });
      }

    }

    // The properties that will be updated should not be optional
    interface IProducts {
      ID?: string;
      name: string;
      description: string;
      brand?: string;
      price?: number;
      currency?: string;
      quantityInStock?: number;
      categoryID?: string;
      supplierID?: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21-23"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataUpdateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataUpdateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onUpdateProduct: async function () {
              // Initialize
              const odata = new ODataUpdateCL(this, "Products"); 

              // set the url parameters
              odata.setUrlParameters({
                "$expand": "toProductLocations"
              });         
            }
          });

        });
    ```

## Refresh After Change

The OData V2 model will be automatically refreshed after the UPDATE request has been completed.

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
    import ODataUpdateCL from "ui5/antares/odata/v2/ODataUpdateCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onUpdateProduct() {
        // Initialize with a type
        const odata = new ODataUpdateCL<IProducts, IProductKeys>(this, "Products"); 

        // deactivate the auto model refresh
        odata.setRefreshAfterChange(false);
      }

    }

    // The properties that will be updated should not be optional
    interface IProducts {
      ID?: string;
      name: string;
      description: string;
      brand?: string;
      price?: number;
      currency?: string;
      quantityInStock?: number;
      categoryID?: string;
      supplierID?: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataUpdateCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataUpdateCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onUpdateProduct: async function () {
              // Initialize
              const odata = new ODataUpdateCL(this, "Products"); 

              // deactivate the auto model refresh
              odata.setRefreshAfterChange(false);      
            }
          });

        });
    ```

## Additional Response Info

The [update()](#update-request) method returns the data of the successfully updated entity. However, you may require further information such as the `status code` and `headers`.

Once the [update()](#update-request) function has been completed, the **getResponse()** method can be utilized to obtain further details.

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

    ``` ts linenums="1" hl_lines="2 3 33 35-37"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataUpdateCL from "ui5/antares/odata/v2/ODataUpdateCL"; // Import the class
    import { IError } from "ui5/antares/types/common"; // Import the error type
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onUpdateProduct() {
        // Initialize with a type
        const odata = new ODataUpdateCL<IProducts, IProductKeys>(this, "Products"); 

        // set the http body
        odata.setData({
          name: "New Product Name",
          description: "New Description"
        });

        try {
          // send the http request and get the result
          const result = await odata.update({
            ID: "3ccb5dd2-cc12-483a-b569-a6ec844f8f0b"
          });

          MessageBox.information(result.ID + " was updated.");

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

    // The properties that will be updated should not be optional
    interface IProducts {
      ID?: string;
      name: string;
      description: string;
      brand?: string;
      price?: number;
      currency?: string;
      quantityInStock?: number;
      categoryID?: string;
      supplierID?: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 36 38-40"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataUpdateCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataUpdateCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onUpdateProduct: async function () {
              // Initialize
              const odata = new ODataUpdateCL(this, "Products"); 

              // set the http body
              odata.setData({
                name: "New Product Name",
                description: "New Description"
              });

              try {
                // send the http request and get the result
                const result = await odata.update({
                  ID: "3ccb5dd2-cc12-483a-b569-a6ec844f8f0b"
                });

                MessageBox.information(result.ID + " was updated.");

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