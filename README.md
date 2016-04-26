# pred-set

Cache your (boolean, not crazy type-theoretic) predicate queries! You just need
to turn `ST` to `IO`, and make `Typeable` instances for your key and (multiple) values:

```haskell
import Data.PredSet.Mutable as P
import Control.Monad.ST


data Foo = -- ...
  deriving Typeable
  
data Bar = -- ...
  deriving Typeable
  
data Key = -- ...
  deriving Typeable
  
  
pred1 :: Key -> Maybe Foo
pred1 = --...

pred2 :: Key -> Maybe Bar
pred2 = -- ...

someKey1 :: Key
someKey1 = -- ...
someKey2 :: Key
someKey2 = -- ...


myPredOperations :: IO (Maybe (Foo, Bar))
myPredOperations = stToIO $ do
  xs <- new
  pred1Ref <- insert pred1 xs
  pred2Ref <- insert pred2 xs
  
  -- Now for lookups:
  
  mFoo  <- P.lookup pred1Ref someKey1 xs
  mBar  <- P.lookup pred2Ref someKey2 xs
  mFoo' <- P.lookup pred1Ref someKey1 xs
  -- not only is mFoo == mFoo', but mFoo''s lookup uses the cache instead!
  
  -- ...
  
  return $ (,) <$> mFoo <*> mBar
```

Great success!
