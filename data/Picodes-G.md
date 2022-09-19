## [Gas - 01] - Use Custom Errors

Instead of using error strings in your oracle contract, to reduce deployment and runtime cost, you should use Custom Errors: see https://blog.soliditylang.org/2021/04/21/custom-errors/. This would save both deployment and runtime cost.