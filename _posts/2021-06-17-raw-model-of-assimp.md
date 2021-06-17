```c++
class RawModel : public Model
{
public:
    RawModel(string const &path)
        :Model()
    {
        loadModel(path);
    }
    void loadModel(string const &path) override
    {
        aiScene scene;
        scene.mRootNode = new aiNode();

        scene.mMaterials = new aiMaterial*[ 1 ];
        scene.mMaterials[ 0 ] = nullptr;
        scene.mNumMaterials = 1;

        scene.mMaterials[ 0 ] = new aiMaterial();

        scene.mMeshes = new aiMesh*[ 1 ];
        scene.mMeshes[ 0 ] = nullptr;
        scene.mNumMeshes = 1;

        scene.mMeshes[ 0 ] = new aiMesh();
        scene.mMeshes[ 0 ]->mMaterialIndex = 0;

        scene.mRootNode->mMeshes = new unsigned int[ 1 ];
        scene.mRootNode->mMeshes[ 0 ] = 0;
        scene.mRootNode->mNumMeshes = 1;

        auto pMesh = scene.mMeshes[ 0 ];

        std::vector<glm::vec3> vertices;
        std::vector<glm::vec3> normals;
        std::vector<glm::vec2> uvs;

        //Default Fill Location Vector                                   
        int draw_order[36] =                                             
            {                                                                
                0,2,1,      2,3,1,                                           
                1,3,5,      3,7,5,                                           
                5,7,4,      7,6,4,                                           
                4,6,0,      6,2,0,                                           
                4,0,5,      0,1,5,                                           
                2,6,3,      6,7,3                                            
            };                                                               

        glm::vec3 data[8] =                                              
            {                                                                
                glm::vec3(-1.0f/2.0f,1.0f/2.0f,1.0f/2.0f),                       
                glm::vec3(1.0f/2.0f,1.0f/2.0f,1.0f/2.0f),                        
                glm::vec3(-1.0f/2.0f,-1.0f/2.0f,1.0f/2.0f),                      
                glm::vec3(1.0f/2.0f,-1.0f/2.0f,1.0f/2.0f),                       
                glm::vec3(-1.0f/2.0f,1.0f/2.0f,-1.0f/2.0f),                      
                glm::vec3(1.0f/2.0f,1.0f/2.0f,-1.0f/2.0f),                       
                glm::vec3(-1.0f/2.0f,-1.0f/2.0f,-1.0f/2.0f),
                glm::vec3(1.0f/2.0f,-1.0f/2.0f,-1.0f/2.0f)  
            };

        for(int i = 0; i < 36; i++)
            {
                vertices.push_back(data[draw_order[i]]);
            }

        //Default Fill Normal Vector
        for(int i = 0; i < 36; i++)
            {
                if(i < 6)       {normals.push_back(glm::vec3(0,0,1));}
                else if(i < 12) {normals.push_back(glm::vec3(1,0,0));}
                else if(i < 18) {normals.push_back(glm::vec3(0,0,-1));}
                else if(i < 24) {normals.push_back(glm::vec3(-1,0,0));}
                else if(i < 30) {normals.push_back(glm::vec3(0,1,0));}
                else if(i < 36) {normals.push_back(glm::vec3(0,- 1,0));}
            }

        //Default Fill UV Vector
        for(int i = 0; i < 6; i++)
            {
                uvs.push_back(glm::vec2(0,1));
                uvs.push_back(glm::vec2(0,0));
                uvs.push_back(glm::vec2(1,1));
                uvs.push_back(glm::vec2(0,0));
                uvs.push_back(glm::vec2(1,0));
                uvs.push_back(glm::vec2(1,1));
            }

        const auto& vVertices = vertices;

        pMesh->mVertices = new aiVector3D[ vVertices.size() ];
        pMesh->mNormals = new aiVector3D[ vVertices.size() ];
        pMesh->mNumVertices = vVertices.size();

        pMesh->mTextureCoords[ 0 ] = nullptr; //new aiVector3D[ vVertices.size() ];
        pMesh->mNumUVComponents[0] = 0;// vVertices.size();

        int j = 0;
        for ( auto itr = vVertices.begin(); itr != vVertices.end(); ++itr ) 
            {
                pMesh->mVertices[ itr - vVertices.begin() ] = aiVector3D( vVertices[j].x, vVertices[j].y, vVertices[j].z );
                pMesh->mNormals[ itr - vVertices.begin() ] = aiVector3D( normals[j].x, normals[j].y, normals[j].z );
                //pMesh->mTextureCoords[0][ itr - vVertices.begin() ] = aiVector3D( uvs[j].x, uvs[j].y, 0 );

                j++;
            }

        pMesh->mFaces = new aiFace[ vVertices.size() / 3 ];
        pMesh->mNumFaces = (unsigned int)(vVertices.size() / 3);

        int k = 0;
        for(int i = 0; i < (vVertices.size() / 3); i++)
            {
                aiFace &face = pMesh->mFaces[i];
                face.mIndices = new unsigned int[3];
                face.mNumIndices = 3;

                face.mIndices[0] = k;
                face.mIndices[1] = k+1;
                face.mIndices[2] = k+2;
                k = k + 3;
            }
        processNode(scene.mRootNode, &scene);
    }
};
```
