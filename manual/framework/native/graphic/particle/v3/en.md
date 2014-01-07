##The Comparison of ParticleSystem between Cocos2d-x-2.x and Cocos2d-x-3.x
---
###CCParticleBatchNode & ParticleBatchNode
###*The Similarity:*
####Inheritance diagram

![](res/./ParticleBatchNode@2.x.png)   ![](res/./ParticleBatchNode@3.0.png)

Both classes are inheritant from Node(or CCNode in v3.x) and TextureProtocol(or CCTextureProtocol in v2.x).

(**Note:** In the rest of this article, we use the notion of v3.x which means all the **CC** prefix will be removed from the class name.)

ParticleBatchNode is like a batch node: if it contains children, it will draw them in one single OpenGL call (often known as "batch draw").

A ParticleBatchNode can reference one and only one texture (one image file or one texture atlas). Only the ParticleSystems which are contained in the texture can be added to the ParticleBatchNode.

All ParticleSystems added to a ParticleBatchNode are drawn in one OpenGL ES draw call.

**WARNING:If the ParticleSystems are not added to a ParticleBatchNode then one OpenGLES draw call will be needed for each ParticleSystem drawing, which is less efficient.**

###*The Differences:*

-  There isn't 'CC' prefix any more.
-  There are plenty of 'override' keywords used for functions which are the new attributes in C++11. As the one below:
	``` virtual void addChild(Node * child) override; ```
-  We use ``` Director::getInstance()``` instead of ```CCDirector::shareDirector() ``` to get the **Singleton**.
-  And we use ``` Director::getInstance()->getTextureCache()->addImage(const std::string &path) ``` to add an image into TextureCache.
-  There are inline functions ``` getTextureAtlas()``` and ```setTextureAtlas(TextureAtlas* atlas)``` used in 3.x version instead of ```CC_SYNTHESIZE(CCTextureAtlas*, m_pTextureAtlas, TextureAtlas); ``` in the elder version. 
-  The function ``` objectAtIndex(unsigned int index);``` is used in 2.x, but you could use ```getObjectAtIndex(long index); ``` in 3.x.
-  There is a ``` BlendFunc::ALPHA_NON_PREMULTIPLIED ``` used in 3.x which is defined as below:
``` const BlendFunc BlendFunc::ALPHA_NON_PREMULTIPLIED = {GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA}; ```
,replacing the two macro definetions as ``` GL_SRC_ALPHA``` and ```GL_ONE_MINUS_SRC_ALPHA``` in 2.x . Also ```BlendFunc::ALPHA_PREMULTIPLIED``` in 3.x is used instead of ```CC_BLEND_SRC``` and ```CC_BLEND_DST ``` .


---
###CCParticleSystem & ParticleSystem
###*The Similarity:*
####Inheritance diagram

![](res/./ParticleSystem@2x.png)	![](res/./ParticleSystem@3.0.png)

Particle System base class. 
Attributes of a Particle System:

- emission rate of the particles
- Gravity Mode (Mode A):
- gravity
- direction
- speed +- variance
- tangential acceleration +- variance
- radial acceleration +- variance
- Radius Mode (Mode B):
- startRadius +- variance
- endRadius +- variance
- rotate +- variance
- Properties common to all modes:
- life +- life variance
- start spin +- variance
- end spin +- variance
- start size +- variance
- end size +- variance
- start color +- variance
- end color +- variance
- life +- variance
- blending function
- texture

Cocos2d supports particles generated by Particle Designer [http://particledesigner.71squared.com/](http://particledesigner.71squared.com/).

'Radius Mode' in Particle Designer uses a fixed emit rate of 30 hz. Since that can't be guaranteed in cocos2d, cocos2d uses a another approach, but the results are almost identical.

cocos2d supports all the variables used by Particle Designer plus a bit more:

- spinning particles (supported when using ParticleSystemQuad)
- tangential acceleration (Gravity mode)
- radial acceleration (Gravity mode)
- radius direction (Radius mode) (Particle Designer supports outwards to inwards direction only)

It is possible to customize any of the above mentioned properties in runtime. Example:

``` emitter.radialAccel = 15; ```
``` emitter.startSpin = 0; ```

###The Differences:
|*Cocos2d-x-2.x*|*Cocos2d-x-3.x*|
|---------------|---------------|
|` kCCParticleDefaultCapacity `|` kParticleDefaultCapacity `|
|` ParticleSystem::Mode kCCParticleModeGravity `|` ParticleSystem::Mode::GRAVITY `
|` ParticleSystem::Mode kCCParticleModeRadius `|` ParticleSystem::Mode::RADIUS `|
|N/A|` typedef ParticleSystem::PositionType tPositionType `|
|` kCCPositionTypeFree ` / ` kPositionTypeFree `|` ParticleSystem::PositionType::FREE `|
|` kCCPositionTypeRelative ` / ` kPositionTypeRelative ` |` ParticleSystem::PositionType::RELATIVE `|
|` kCCPositionTypegRrouped ` / ` kPositionTypegRrouped `|` ParticleSystem::PositionType::GROUPED `|
|` kCCParticleDurationInfinity ` / ` kParticleDurationInfinity `|` ParticleSystem::DURATION_INFINITY `|
|` kCCParticleStartSizeEqualToEndSize ` / ` kParticleStartSizeEqualToEndSize `|` ParticleSystem::START_SIZE_EQUAL_TO_END_SIZE `|
|` kCCParticleStartRadiusEqualToEndRadius ` / ` kParticleStartRadiusEqualToEndRadius `|` ParticleSystem::START_RADIUS_EQUAL_TO_END_RADIUS `|

- More than 20 inline function are used in this head file to replace the use of macros like ``` CC_PROPERTY(varType, varName, funName)```  and ```CC_PROPERTY_READONLY(varType, varName, funName) ```

|*Cocos2d-x-2.x*|*Cocos2d-x-3.x*|
|---------------|---------------|
|` CC_PROPERTY(int, m_nEmitterMode, EmitterMode) `|`Mode _emitterMode;`	`inline Mode getEmitterMode() const { return _emitterMode; };`	`inline void setEmitterMode(Mode mode) { _emitterMode = mode; };`|
|` CC_PROPERTY(CCParticleBatchNode*, m_pBatchNode, BatchNode) `|`ParticleBatchNode* _batchNode;`	`virtual ParticleBatchNode* getBatchNode() const;`	`virtual void setBatchNode(ParticleBatchNode* batchNode);`|
|` CC_PROPERTY_READONLY(unsigned int, m_uParticleCount, ParticleCount) `|`int _particleCount;`	`inline unsigned int getParticleCount() const { return _particleCount; };`|
|` CC_PROPERTY(float, m_fDuration, Duration) `|`float _duration;`	`inline float getDuration() const { return _duration; };`	`inline void setDuration(float duration) { _duration = duration; };`|
|` CC_PROPERTY_PASS_BY_REF(CCPoint, m_tSourcePosition, SourcePosition) `|`Point _sourcePosition;`	`inline const Point& getSourcePosition() const { return _sourcePosition; };`	`inline void setSourcePosition(const Point& pos) { _sourcePosition = pos; };`|
|` CC_PROPERTY_PASS_BY_REF(CCPoint, m_tPosVar, PosVar) `|`Point _posVar;`	`inline const Point& getPosVar() const { return _posVar; };`	`inline void setPosVar(const Point& pos) { _posVar = pos; };`|
|` CC_PROPERTY(float, m_fLife, Life) `|`float _life;`	`inline float getLife() const { return _life; };`	`inline void setLife(float life) { _life = life; };`|
|` CC_PROPERTY(float, m_fLifeVar, LifeVar) `|`float _lifeVar;`	`inline float getLifeVar() const { return _lifeVar; };`	`inline void setLifeVar(float lifeVar) { _lifeVar = lifeVar; };`|
|` CC_PROPERTY(float, m_fAngle, Angle) `|`float _angle;`	`inline float getAngle() const { return _angle; };`	`inline void setAngle(float angle) { _angle = angle; };`|
|` CC_PROPERTY(float, m_fAngleVar, AngleVar) `|`float _angleVar;`	`inline float getAngleVar() const { return _angleVar; };`	`inline void setAngleVar(float angleVar) { _angleVar = angleVar; };`|
|` CC_PROPERTY(float, m_fStartSize, StartSize) `|`float _startSize;`	`inline float getStartSize() const { return _startSize; };`	`inline void setStartSize(float startSize) { _startSize = startSize; };`|
|` CC_PROPERTY(float, m_fStartSizeVar, StartSizeVar) `|`float _startSizeVar;`	`inline float getStartSizeVar() const { return _startSizeVar; };`	`inline void setStartSizeVar(float sizeVar) { _startSizeVar = sizeVar; };`|
|` CC_PROPERTY(float, m_fEndSize, EndSize) `|`float _endSize;`	`inline float getEndSize() const { return _endSize; };`	`inline void setEndSize(float endSize) { _endSize = endSize; };`|
|` CC_PROPERTY(float, m_fEndSizeVar, EndSizeVar) `|`float _endSizeVar;`	`inline float getEndSizeVar() const { return _endSizeVar; };`	`inline void setEndSizeVar(float sizeVar) { _endSizeVar = sizeVar; };`|
|` CC_PROPERTY_PASS_BY_REF(ccColor4F, m_tStartColor, StartColor) `|`Color4F _startColor;`	`inline const Color4F& getStartColor() const { return _startColor; };`	`inline void setStartColor(const Color4F& color) { _startColor = color; };`|
|` CC_PROPERTY_PASS_BY_REF(ccColor4F, m_tStartColorVar, StartColorVar) `|`Color4F _startColorVar;`	`inline const Color4F& getStartColorVar() const { return _startColorVar; };`	`inline void setStartColorVar(const Color4F& color) { _startColorVar = color; };`|
|` CC_PROPERTY_PASS_BY_REF(ccColor4F, m_tEndColorVar, EndColorVar) `|`Color4F _endColorVar;`	`inline const Color4F& getEndColor() const { return _endColor; };`	`inline void setEndColor(const Color4F& color) { _endColor = color; };`|
|` CC_PROPERTY(float, m_fStartSpin, StartSpin) `|`float _startSpin;`	`inline float getStartSpin() const { return _startSpin; };`	`inline void setStartSpin(float spin) { _startSpin = spin; };`|
|` CC_PROPERTY(float, m_fStartSpinVar, StartSpinVar) `|`float _startSpinVar;`	`inline float getStartSpinVar() const { return _startSpinVar; };`	`inline void setStartSpinVar(float pinVar) { _startSpinVar = pinVar; };`|
|` CC_PROPERTY(float, m_fEndSpin, EndSpin) `|`float _endSpinVar;`	`inline float getEndSpin() const { return _endSpin; };`	`inline void setEndSpin(float endSpin) { _endSpin = endSpin; };`|
|` CC_PROPERTY(float, m_fEndSpinVar, EndSpinVar) `|`float _endSpinVar;`	`inline float getEndSpinVar() const { return _endSpinVar; };`	`inline void setEndSpinVar(float endSpinVar) { _endSpinVar = endSpinVar; };`|
|` CC_PROPERTY(float, m_fEmissionRate, EmissionRate) `|`float _emissionRate;`	`inline float getEmissionRate() const { return _emissionRate; };`	`inline void setEmissionRate(float rate) { _emissionRate = rate; };`|
|` CC_PROPERTY(unsigned int, m_uTotalParticles, TotalParticles) `|`int _totalParticles;`	`virtual int getTotalParticles() const;`	`virtual void setTotalParticles(int totalParticles);`|
|` CC_PROPERTY(CCTexture2D*, m_pTexture, Texture) `|`Texture2D* _texture;`	`virtual Texture2D* getTexture() const override;`	`virtual void setTexture(Texture2D *texture) override;`|
|` CC_PROPERTY(ccBlendFunc, m_tBlendFunc, BlendFunc) `|`BlendFunc _blendFunc;`	`virtual void setBlendFunc(const BlendFunc &blendFunc) override;`	`virtual const BlendFunc &getBlendFunc() const override;`|
|` CC_PROPERTY(bool, m_bOpacityModifyRGB, OpacityModifyRGB) `|`bool _opacityModifyRGB;`	`inline void setOpacityModifyRGB(bool opacityModifyRGB) { _opacityModifyRGB = opacityModifyRGB; };`	`inline bool isOpacityModifyRGB() const { return _opacityModifyRGB; };`	`CC_DEPRECATED_ATTRIBUTE inline bool getOpacityModifyRGB() const { return isOpacityModifyRGB(); }`|
|` CC_PROPERTY(tCCPositionType, m_ePositionType, PositionType) `|`PositionType _positionType;`	`inline PositionType getPositionType() const { return _positionType; };`	`inline void setPositionType(PositionType type) { _positionType = type; };`|
|` CC_SYNTHESIZE(unsigned int, m_uAtlasIndex, AtlasIndex) `|`int _atlasIndex;` `inline int getAtlasIndex() const { return _atlasIndex; };`	`inline void setAtlasIndex(int index) { _atlasIndex = index; };`|

- Two new attributes ``` std::string _configName;``` and ```int _yCoordFlipped; ``` are used in 3.x.


---
###CCParticleSystemQuad & ParticleSystemQuad
###*The Similarity:*
####Inheritance diagram

![](res/./ParticleSystem@2x.png)	![](res/./ParticleSystem@3.0.png)

ParticleSystemQuad is a subclass of ParticleSystem.
It includes all the features of ParticleSystem.
Special features and Limitations:

- Particle size can be any float number.
- The system can be scaled
- The particles can be rotated
- It supports subrects
- It supports batched rendering since 1.1

###The Differences:

There aren't much more changes in 3.x, you can just use it in 3.x as you do it in 2.x. You can get more details in the 
[Reference](http://cocos2d-x.org/wiki/Reference) .


###*The Sample:*
- There is a sample using ParticleSystem below:

![](res/./ParticleSystemEffect.png)

	/*
		// Cocos2d-x-2.x
		CCSize size = CCDirector::sharedDirector()->getWinSize();
		ParticleSystemQuad* m_emitter = ParticleSystemQuad::createWithTotalParticles(900);
		m_emitter->setTexture(CCTextureCache::sharedTextureCache()->addImage("fire.png"));
	*/
		// Cocos2d-x-3.x
		auto size = Director::getInstance()->getWinSize();
		auto m_emitter = ParticleSystemQuad::createWithTotalParticles(900);
		m_emitter->setTexture(Director::getInstance()->getTextureCache()->addImage("fire.png"));
	
		//The code below we can use both in 2.x and 3.x 
		m_emitter->setDuration(-1);
		m_emitter->setGravity(Point(0, -240));  // in Cocos2d-x-2.x CCPoint(0, -240) is used
		m_emitter->setAngle(90);
		m_emitter->setAngleVar(360);
		m_emitter->setRadialAccel(50);
		m_emitter->setRadialAccelVar(0);
		m_emitter->setTangentialAccel(30);
		m_emitter->setTangentialAccelVar(0);
		m_emitter->setPosition(Point(size.width / 2, size.height));
		m_emitter->setPosVar(Point(400, 0));
		m_emitter->setLife(4);
		m_emitter->setLifeVar(2);
		m_emitter->setStartSpin(30);
		m_emitter->setStartSpinVar(60);
		m_emitter->setEndSpin(60);
		m_emitter->setEndSpinVar(60);
		m_emitter->setStartColor(Color4F(255,255,255,1));
		m_emitter->setStartColorVar(Color4F(0,0,0,0));
		m_emitter->setEndColor(Color4F(255, 255, 255, 1));
		m_emitter->setEndColorVar(Color4F(0, 0, 0, 0));
		m_emitter->setStartSize(30);
		m_emitter->setStartSizeVar(0);
		m_emitter->setEndSize(20.0f);
		m_emitter->setEndSizeVar(0);
		m_emitter->setEmissionRate(100);
		addChild(m_emitter,10);

- There are also some subclasses you can use in your program,such as ParticleExplosion, ParticleFire, ParticleFireworks, ParticleFlower, ParticleGalaxy, ParticleMeteor, ParticleRain, ParticleSmoke, ParticleSnow, ParticleSpiral, ParticleSun. You can use them to create common particle effects easily which is really awesome.
