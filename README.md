# data
Data set(Open source)



{
  "openapi": "3.0.2",
  "info": {
    "title": "集简云 AI Actions for GPT (Dynamic)",
    "version": "1.0.0",
    "description": "Equip GPTs with the ability to run thousands of actions via 集简云."
  },
  "servers": [
    {
      "url": "https://chat.jijyun.cn"
    }
  ],
  "paths": {
    "/v1/openapi/exposed": {
      "get": {
        "operationId": "list_available_actions",
        "summary": "List Available Actions",
        "parameters": [
          
        ],
        "responses": {
          "200": {
            "description": "OK",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/AvailableActionResponseSchema"
                }
              }
            }
          }
        },
        "description": "List all the currently available actions for the user. If you try to run an action and receive an error\n that it does not exist, try refreshing this list first.",
        "security": [
          {
            "AccessPointApiKeyHeader": [
              
            ]
          }
        ]
      }
    },
    "/v1/openapi/exposed/{available_action_id}/execute_v2/": {
      "post": {
        "operationId": "run_action",
        "summary": "Run Action",
        "parameters": [
          {
            "in": "path",
            "name": "available_action_id",
            "schema": {
              "title": "Available Action Id",
              "type": "string",
              "pattern": ".*_jjyibotID_.*",
              "example": "62_326_jjyibotID_jjy_robot_1001"
            },
            "required": true,
            "example": "62_326_jjyibotID_jjy_robot_1001"
          }
        ],
        "responses": {
          "200": {
            "description": "OK",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/RunResponse"
                }
              }
            }
          },
          "400": {
            "description": "Bad Request",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ErrorResponse"
                }
              }
            }
          }
        },
        "description": "Run an available action using data. You will include associated params from list_available_actions in the data of the request.",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/RunRequest"
              }
            }
          },
          "required": true
        },
        "security": [
          {
            "AccessPointApiKeyHeader": [
              
            ]
          }
        ]
      }
    }
  },
  "components": {
    "schemas": {
      "AvailableActionSchema": {
        "title": "AvailableActionSchema",
        "type": "object",
        "properties": {
          "id": {
            "title": "Id",
            "description": "The unique ID of the available action.",
            "type": "string"
          },
          "operation_id": {
            "title": "Operation Id",
            "description": "The operation ID of the available action.",
            "type": "string"
          },
          "description": {
            "title": "Description",
            "description": "Description of the action.",
            "type": "string"
          },
          "params": {
            "title": "Params",
            "description": "Available hint fields for the action.",
            "type": "object"
          }
        },
        "required": [
          "id",
          "operation_id",
          "description",
          "params"
        ]
      },
      "AvailableActionResponseSchema": {
        "title": "AvailableActionResponseSchema",
        "type": "object",
        "properties": {
          "results": {
            "title": "Results",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/AvailableActionSchema"
            }
          },
          "configuration_link": {
            "title": "Configuration Link",
            "description": "Guide the user to setup new actions with the configuration_link. You can optionally add ?setup_action=... onto configuration_link to set up a specific 集简云 app and action For example: https://actions.jijyun.cn/gpt/start?setup_action=gmail find email",
            "type": "string"
          }
        },
        "required": [
          "results",
          "configuration_link"
        ]
      },
      "RunResponse": {
        "title": "RunResponse",
        "description": "This is a summary of the results given the action that was run.",
        "type": "object",
        "properties": {
          "id": {
            "title": "Id",
            "description": "The id of the run log.",
            "type": "string"
          },
          "action_used": {
            "title": "Action Used",
            "description": "The name of the action that was run.",
            "type": "string"
          },
          "input_params": {
            "title": "Input Params",
            "description": "The params we used / will use to run the action.",
            "type": "object"
          },
          "review_url": {
            "title": "Review Url",
            "description": "The URL to run the action or review the AI choices the AI made for input_params given instructions.",
            "type": "string"
          },
          "result": {
            "title": "Result",
            "description": "A trimmed down result of the first item of the full results. Ideal for humans and language models!",
            "type": "object"
          },
          "additional_results": {
            "title": "Additional Results",
            "description": "The rest of the full results. Always returns an array of objects",
            "type": "array",
            "items": {
              "type": "object"
            }
          },
          "result_field_labels": {
            "title": "Result Field Labels",
            "description": "Human readable labels for some of the keys in the result.",
            "type": "object"
          },
          "status": {
            "title": "Status",
            "description": "The status of the action run.",
            "default": "success",
            "enum": [
              "success",
              "error",
              "empty",
              "preview"
            ],
            "type": "string"
          },
          "error": {
            "title": "Error",
            "description": "The error message if the action run failed.",
            "type": "string"
          },
          "assistant_hint": {
            "title": "Assistant Hint",
            "description": "A hint for the assistant on what to do next.",
            "type": "string"
          },
          "full_results": {
            "title": "Full Results",
            "description": "The full results, not summarized, if available. Always returns an array of objects.",
            "type": "array",
            "items": {
              "type": "object"
            }
          }
        },
        "required": [
          "id",
          "action_used",
          "input_params",
          "review_url",
          "additional_results",
          "full_results"
        ]
      },
      "ErrorResponse": {
        "title": "ErrorResponse",
        "type": "object",
        "properties": {
          "error": {
            "title": "Error",
            "description": "Error message.",
            "type": "string"
          }
        },
        "required": [
          "error"
        ]
      },
      "RunRequest": {
        "title": "RunRequest",
        "description": "Try and stuff as much relevant information into the data as possible. This type of action allows optionally setting preview_only if the user wants to preview before running.",
        "type": "object",
        "properties": {
          "data": {
            "title": "Data",
            "description": "The params we used / will use to run the action",
            "type": "string"
          },
          "preview_only": {
            "title": "Preview Only",
            "description": "If true, we will not run the action, but will do a dry-run and return a preview for the user to confirm.",
            "default": false,
            "type": "boolean"
          }
        },
        "required": [
          "data"
        ]
      }
    },
    "securitySchemes": {
      "AccessPointApiKeyHeader": {
        "type": "apiKey",
        "in": "header",
        "name": "Authorization"
      }
    }
  }
}
