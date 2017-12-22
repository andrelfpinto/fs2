Here is how you add numbers:
```tut
import fs2._
import cats.effect.IO

val pureStream = Stream(1, 2, 3)
val ioStream = Stream(1, 2, 3).covary[IO]

Stream.apply(1, 2, 3).toVector

Stream.attemptEval(IO(1)).runLast.unsafeRunSync
Stream.attemptEval(IO(println("ran!"))).runLast.unsafeRunSync
Stream.attemptEval(IO(new Exception("oh noes!"))).runLast.unsafeRunSync
Stream.attemptEval(IO(throw new Exception("oh noes!"))).runLast.unsafeRunSync

val err = Stream.fail(new Exception("oh noes!"))
val count = new java.util.concurrent.atomic.AtomicLong(0)
val acquire = IO { println("incremented: " + count.incrementAndGet); () }
val release = IO { println("decremented: " + count.decrementAndGet); () }

Stream.bracket(acquire)(_ => Stream(1, 2, 3) ++ err, _ => release).run.unsafeRunSync()

count

Stream.chunk(Chunk(1, 2, 3)).toVector

Stream.constant(1).take(3).toVector
Stream.constant(1, segmentSize = 2).take(3).toVector

Stream.duration
Stream.duration[IO].take(3).runLog.unsafeRunSync()

Stream.emit(1).toVector

Stream.emit(Vector(1, 2, 3)).toVector

Stream.emits(Vector(1, 2, 3)).toVector

Stream.empty.toVector

Stream.eval(IO(1)).runLast.unsafeRunSync
Stream.eval(IO(println("ran!"))).runLast.unsafeRunSync
Stream.eval(IO(throw new Exception("oh noes!"))).runLast.unsafeRunSync
Stream.eval(IO(1)).runLast.attempt.unsafeRunSync
Stream.eval(IO(throw new Exception("oh noes!"))).runLast.attempt.unsafeRunSync

Stream.eval_(IO(1)).runLast.unsafeRunSync
Stream.eval_(IO(println("ran!"))).runLast.unsafeRunSync
Stream.eval_(IO(throw new Exception("oh noes!"))).runLast.unsafeRunSync

//Stream.every

Stream.fail(new Exception("oh noes!")).toVector

Stream.force(IO(Stream(1, 2, 3).covary[IO])).runLog.unsafeRunSync
//Stream.force(IO(ioStream)).runLog.unsafeRunSync

Stream.iterate(1)(i => i + 1).take(3).toVector

Stream.iterateEval(1)(i => IO(i + 1)).take(3).runLog.unsafeRunSync

Stream.range(1, 5, 2).toVector
Stream.range(1, 6, 2).toVector

Stream.ranges(1, 6, 2).toVector

Stream.repeatEval(IO(1)).take(3).runLog.unsafeRunSync

Stream.segment(Segment.from(1)).take(3).toVector

//import scala.util.Random
//
//Random.setSeed(0L)
//def nextInt = Random.nextInt
//
//Stream.suspend {
//  Stream.emit( => nextInt).repeat
//}
//
//Stream.suspend {
//  val digest = java.security.MessageDigest.getInstance("SHA-256")
//  val bytes: Stream[Pure,Byte] = ???
//  bytes.chunks.fold(digest) { (d,c) => d.update(c.toBytes.values); d }
//}

Stream.unfold(1)(i => if (i < 4) Some(((i, i), (i + 1))) else None).toVector

Stream.unfoldSegment(1)(i => if (i < 4) Some((Segment((i, i)), (i + 1))) else None).toVector

Stream.unfoldEval(1)(i => if (i < 4) IO(Some(((i, i), (i + 1)))) else IO(None)).runLog.unsafeRunSync

Stream.unfoldSegmentEval(1)(i => if (i < 4) IO(Some((Segment((i, i)), (i + 1)))) else IO(None)).runLog.unsafeRunSync

Stream.unfoldChunkEval(1)(i => if (i < 4) IO(Some((Chunk((i, i)), (i + 1)))) else IO(None)).runLog.unsafeRunSync
Stream.unfoldChunkEval(1)(i => if (i < 4) IO(Some((Chunk(i, i), (i + 1)))) else IO(None)).runLog.unsafeRunSync



Stream(1, 2, 3).as(0).toList

(Stream(1, 2, 3) ++ Stream.fail(new RuntimeException) ++ Stream(4, 5)).attempt.toList


val buf = new scala.collection.mutable.ListBuffer[String]()
Stream.range(0, 100).covary[IO].
  evalMap(i => IO { buf += s">$i"; i }).
  buffer(3).
  evalMap(i => IO { buf += s"<$i"; i }).
  take(0).
  runLog.unsafeRunSync
buf.toList

val buf = new scala.collection.mutable.ListBuffer[String]()
Stream.range(0, 100).covary[IO].
  evalMap(i => IO { buf += s">$i"; i }).
  buffer(3).
  evalMap(i => IO { buf += s"<$i"; i }).
  take(1).
  runLog.unsafeRunSync
buf.toList

val buf = new scala.collection.mutable.ListBuffer[String]()
Stream.range(0, 100).covary[IO].
  evalMap(i => IO { buf += s">$i"; i }).
  buffer(3).
  evalMap(i => IO { buf += s"<$i"; i }).
  take(2).
  runLog.unsafeRunSync
buf.toList

val buf = new scala.collection.mutable.ListBuffer[String]()
Stream.range(0, 100).covary[IO].
  evalMap(i => IO { buf += s">$i"; i }).
  buffer(3).
  evalMap(i => IO { buf += s"<$i"; i }).
  take(3).
  runLog.unsafeRunSync
buf.toList

val buf = new scala.collection.mutable.ListBuffer[String]()
Stream.range(0, 100).covary[IO].
  evalMap(i => IO { buf += s"a: $i"; i }).
  evalMap(i => IO { buf += s"b: $i"; i }).
  buffer(3).
  evalMap(i => IO { buf += s"c: $i"; i }).
  take(4).
  runLog.unsafeRunSync
buf.toList

//val buf = new scala.collection.mutable.ListBuffer[String]()
//Stream.range(0, 100).covary[IO].
//  evalMap(i => IO { buf += s"a: $i"; i }).
//  buffer(4).
//  evalMap(i => IO { buf += s"b: $i"; i }).
//  buffer(3).
//  evalMap(i => IO { buf += s"c: $i"; i }).
//  buffer(2).
//  take(12).
//  runLog.unsafeRunSync
//buf.toList
//
//buf.toList.foldLeft(("", "a")) { case ((acc, pre), now) =>
//  if (now.startsWith(pre)) {
//    if(acc == "") (s"$now", pre)
//    else (s"$acc, $now", pre)
//  } else (s"$acc\n$now", now.take(1))
//}._1

val buf = new scala.collection.mutable.ListBuffer[String]()
Stream.range(0, 10).covary[IO].
  evalMap(i => IO { buf += s">$i"; i }).
  bufferAll.
  evalMap(i => IO { buf += s"<$i"; i }).
  take(4).
  runLog.unsafeRunSync
buf.toList

val buf = new scala.collection.mutable.ListBuffer[String]()
Stream.range(0, 10).covary[IO].
  evalMap(i => IO { buf += s">$i"; i }).
  bufferBy(_ % 3 == 0).
  evalMap(i => IO { buf += s"<$i"; i }).
  runLog.unsafeRunSync
buf.toList

buf.toList.foldLeft(("", ">")) { case ((acc, pre), now) =>
  if (now.startsWith(pre)) {
    if(acc == "") (s"$now", pre)
    else (s"$acc, $now", pre)
  } else (s"$acc\n$now", now.take(1))
}._1









/*
Stream.getClass.getMethods.map(_.getName).sorted.foreach(println)

apply
as$extension
attempt$extension
attemptEval
bracket
bracketWithToken
buffer$extension
bufferAll$extension
bufferBy$extension
changesBy$extension
chunk
chunkLimit$extension
chunks$extension
collect$extension
collectFirst$extension
cons$extension
cons1$extension
consChunk$extension
constant
constant$default$2
covaryOutput$extension
covaryPure
covaryPurePipe
covaryPurePipe2
delete$extension
drain$extension
drop$extension
dropLast$extension
dropLastIf$extension
dropRight$extension
dropThrough$extension
dropWhile$extension
duration
emit
emits
empty
empty_
equals
equals$extension
eval
eval_
every
exists$extension
fail
filter$extension
filterWithPrevious$extension
find$extension
fold$extension
fold1$extension
foldMap$extension
forall$extension
force
fromFreeC
get$extension
getClass
groupBy$extension
hashCode
hashCode$extension
head$extension
intersperse$extension
iterate
iterateEval
last$extension
lastOr$extension
map$extension
mapAccumulate$extension
mapChunks$extension
mapSegments$extension
mask$extension
monoidInstance
noneTerminate$extension
notify
notifyAll
range
range$default$3
ranges
reduce$extension
repeat$extension
repeatEval
rethrow$extension
scan$extension
scan1$extension
scan_$extension
scope$extension
segment
segmentLimit$extension
segmentN$default$2$extension
segmentN$extension
segments$extension
sliding$extension
split$extension
suspend
syncInstance
tail$extension
take$extension
takeRight$extension
takeThrough$extension
takeWhile$default$2$extension
takeWhile$extension
toString
toString$extension
unNone$extension
unNoneTerminate$extension
unchunk$extension
unfold
unfoldChunkEval
unfoldEval
unfoldSegment
unfoldSegmentEval
wait
wait
wait
zipWithIndex$extension
zipWithNext$extension
zipWithPrevious$extension
zipWithPreviousAndNext$extension
zipWithScan$extension
zipWithScan1$extension
*/

/*
Stream().getClass.getMethods.map(_.getName).sorted.foreach(println)

as
as$extension
attempt
attempt$extension
attemptEval
bracket
buffer
buffer$extension
bufferAll
bufferAll$extension
bufferBy
bufferBy$extension
changesBy
changesBy$extension
chunk
chunkLimit
chunkLimit$extension
chunks
chunks$extension
collect
collect$extension
collectFirst
collectFirst$extension
cons
cons$extension
cons1
cons1$extension
consChunk
consChunk$extension
constant
constant$default$2
covaryOutput
covaryOutput$extension
covaryPure
covaryPurePipe
covaryPurePipe2
delete
delete$extension
drain
drain$extension
drop
drop$extension
dropLast
dropLast$extension
dropLastIf
dropLastIf$extension
dropRight
dropRight$extension
dropThrough
dropThrough$extension
dropWhile
dropWhile$extension
duration
emit
emits
empty
equals
equals$extension
eval
eval_
every
exists
exists$extension
fail
filter
filter$extension
filterWithPrevious
filterWithPrevious$extension
find
find$extension
fold
fold$extension
fold1
fold1$extension
foldMap
foldMap$extension
forall
forall$extension
force
fs2$Stream$$free
get
get$extension
getClass
groupBy
groupBy$extension
hashCode
hashCode$extension
head
head$extension
intersperse
intersperse$extension
iterate
iterateEval
last
last$extension
lastOr
lastOr$extension
map
map$extension
mapAccumulate
mapAccumulate$extension
mapChunks
mapChunks$extension
mapSegments
mapSegments$extension
mask
mask$extension
monoidInstance
noneTerminate
noneTerminate$extension
notify
notifyAll
range
range$default$3
ranges
reduce
reduce$extension
repeat
repeat$extension
repeatEval
rethrow
rethrow$extension
scan
scan$extension
scan1
scan1$extension
scan_$extension
scope
scope$extension
segment
segmentLimit
segmentLimit$extension
segmentN
segmentN$default$2
segmentN$default$2$extension
segmentN$extension
segments
segments$extension
sliding
sliding$extension
split
split$extension
suspend
syncInstance
tail
tail$extension
take
take$extension
takeRight
takeRight$extension
takeThrough
takeThrough$extension
takeWhile
takeWhile$default$2
takeWhile$default$2$extension
takeWhile$extension
toString
toString$extension
unNone
unNone$extension
unNoneTerminate
unNoneTerminate$extension
unchunk
unchunk$extension
unfold
unfoldChunkEval
unfoldEval
unfoldSegment
unfoldSegmentEval
wait
wait
wait
zipWithIndex
zipWithIndex$extension
zipWithNext
zipWithNext$extension
zipWithPrevious
zipWithPrevious$extension
zipWithPreviousAndNext
zipWithPreviousAndNext$extension
zipWithScan
zipWithScan$extension
zipWithScan1
zipWithScan1$extension
*/
```
