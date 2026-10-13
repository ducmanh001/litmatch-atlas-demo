# REST API of media

id: api:media
module: module:media
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/media.md

### POST /media/images/upload-intent

Xin presigned PUT URL + assetId (purpose post/message/story); kiểm content type cho phép, sizeBytes <= MEDIA_IMAGE_MAX_BYTES, chặn guest.

- handler: MediaController.createImageUploadIntent (apps/core-api/src/modules/media/media.controller.ts:24); operationId: MediaController_createImageUploadIntent
- auth: Bearer JWT
- body CreateImageUploadIntentDto: purpose*:enum(post|message|story), contentType*:string, sizeBytes*:number[minimum=1 maximum=25000000]
- response 201 ImageUploadIntentDto: assetId:string, uploadUrl:string, publicUrl:string, expiresAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

